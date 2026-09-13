基于你提供的文本，结合 RK3588（ARM64）的硬件特性，我将这份文档进行**深度专业化重构与细化**。特别是你强调的**驱动部分**，我会从 Linux 设备模型（Device Model）、设备树（OF）解析、`initcall` 机制以及 `EPROBE_DEFER`（延迟探测）机制进行内核级的剖析。

---

# U-Boot 交接后 → Linux Kernel 启动与驱动初始化全流程（RK3588 深度解析）

U-Boot 完成 `booti` 后，CPU0 处于 EL2（或 EL1），MMU 关闭。U-Boot 传递参数：
*   **x0** = DTB 物理地址（8字节对齐，≤2MB）
*   **x1~x3** = 0（保留）
*   **CPU状态**：中断禁用，D-cache 关闭，I-cache 可选开启。

## 1. 内核入口与早期汇编初始化（`arch/arm64/kernel/head.S`）

入口点 `primary_entry`（Image 头部 Magic `0x644d5241`），执行严格的底层硬件初始化：

1.  **保存与校验**：保存 DTB 地址至 `x21`，校验 DTB 合法性（Magic `0xd00dfeed`）。
2.  **EL 级别处理 (`el2_setup`)**：
    *   若在 EL2，配置 `HCR_EL2`（Hypervisor 配置）、`CNTHCTL_EL2`（定时器）、`VTTBR_EL2`（虚拟化页表）等，随后通过 `eret` 降级至 EL1。
    *   配置 `SCTLR_EL1`（系统控制寄存器），如启用 I-cache、禁用 D-cache、设置对齐检查。
3.  **创建早期页表 (`__create_page_tables`)**：
    *   建立 **Identity Mapping**（`idmap_pg_dir`）：确保开启 MMU 瞬间 PC 指针不飞。
    *   建立 **Kernel Image Mapping**（`init_pg_dir`）：映射内核镜像区域。
    *   *注：此时页表粒度通常为 4KB（或 64KB，取决于内核配置 `CONFIG_ARM64_64K_PAGES`）。*
4.  **开启 MMU (`__primary_switch` → `enable_mmu`)**：
    *   设置 `TTBR0_EL1`（用户空间/Identity）和 `TTBR1_EL1`（内核空间）。
    *   置位 `SCTLR_EL1.M`，正式开启内存管理单元。
5.  **切换到虚拟地址空间 (`__primary_switched`)**：
    *   设置异常向量表 `VBAR_EL1`。
    *   建立初始内核栈（`init_thread_union`）。
    *   清零 BSS 段。
    *   跳转至 C 语言入口 **`start_kernel()`**。
    *   *此时仅 CPU0 运行，其余 7 核（4×A76 + 4×A55）处于复位/等待状态。*

## 2. 内核核心初始化与 Initcall 机制（`start_kernel()`）

`start_kernel()` 是架构无关的入口，按极其严格的顺序执行：

| 阶段 | 关键函数 | 作用与 RK3588 特性 |
| :--- | :--- | :--- |
| **早期准备** | `setup_arch()` | **解析 DTB**（`setup_machine_fdt`）、初始化 `memblock`（内存块分配器）、`earlycon`（早期串口 `feb50000.serial`）。 |
| **内存管理** | `mm_init()` / `mem_init()` | 建立伙伴系统（Buddy System）、SLUB 分配器、`vmalloc` 空间。 |
| **调度与中断** | `sched_init()` / `init_IRQ()` | 初始化进程调度器；初始化 **GICv3**（RK3588 使用 GIC-600）。 |
| **时钟与定时器** | `time_init()` | 初始化 ARM Arch Timer，读取 DTB 中的 `clock-frequency`。 |
| **SMP 准备** | `smp_prepare_cpus()` | 解析 DTB 中的 `enable-method = "psci"`，准备唤醒从核。 |
| **驱动模型初始化** | `driver_init()` | 初始化 `bus`、`class`、`device`、`driver` 等 kobject，建立 sysfs 骨架。 |
| **收尾与多核** | `rest_init()` | 创建 PID 1 (`kernel_init`) 和 PID 2 (`kthreadd`)，随后调用 `smp_init()` 唤醒从核。 |

## 3. 驱动模型与设备初始化（核心深化部分）

Linux 内核启动最复杂的部分在于**驱动的探测与绑定**。RK3588 的驱动初始化分为以下几个关键层级：

### 3.1 设备树 (DTB) 到设备的转换
在 `setup_arch()` 中，内核调用 `unflatten_device_tree()` 将 U-Boot 传来的扁平化 DTB 展开为内核的 `struct device_node` 树。
随后，在 `initcalls` 的 `arch_initcall` 阶段，调用 `of_platform_default_populate()`。该函数遍历 DTB 中的所有节点，将符合 `compatible` 属性且位于 `simple-bus` 下的节点，转换为 **`platform_device`**。

### 3.2 Initcall 机制（驱动初始化的引擎）
内核通过 `do_initcalls()` 按优先级依次执行编译进内核的初始化函数。优先级从高到低：
*   `pure_initcall` / `core_initcall` / `postcore_initcall`
*   `arch_initcall`（架构相关，如 GIC、Timer 初始化）
*   `subsys_initcall`（子系统，如 PCIe、GPIO、Clk 子系统）
*   `fs_initcall`（文件系统）
*   `device_initcall`（**绝大多数外设驱动在此注册**，如 RK3588 的 PCIe、NPU、GPU）
*   `late_initcall`（最后收尾）

### 3.3 驱动与设备的匹配与 Probe
1.  **注册**：驱动通过 `module_platform_driver()` 或 `builtin_platform_driver()` 将 `struct platform_driver` 注册到 `platform_bus_type` 中。
2.  **匹配**：内核比对 `platform_device` 的 `compatible` 字符串与 `platform_driver` 的 `of_match_table`。
3.  **绑定**：匹配成功后，调用 `really_probe()` -> 执行驱动的 `probe()` 函数。
4.  **Rockchip 特有驱动初始化时序**：
    *   **Clk 驱动** (`rockchip_clk_init`)：最先初始化，建立时钟树。
    *   **Pinctrl 驱动** (`pinctrl-rockchip`)：初始化 IOMUX，配置引脚复用。
    *   **PMU 驱动** (`rockchip_pmu`)：初始化电源域，为后续设备上电。
    *   **PCIe/NPU/GPU 驱动**：依赖 Clk、Pinctrl、PMU 和 Regulator（电源管理）。

### 3.4 `EPROBE_DEFER`（延迟探测机制）
在 RK3588 中，这是一个极其重要的机制。
如果 NPU 驱动在 `probe` 时，发现它依赖的时钟（Clk）或电源（Regulator）驱动**尚未加载**，它不会报错退出，而是返回 `-EPROBE_DEFER`。
内核会将该驱动放入 **`deferred_probe_pending_list`** 链表。当新的驱动/资源注册成功后，会触发 `driver_deferred_probe_trigger()`，重新尝试 `probe` 该设备。这就是为什么串口日志中经常看到 `probe deferred` 提示，但最终设备都能正常工作的原因。

## 4. 多核启动（SMP Bring-up on RK3588）

在 `rest_init()` 之后，主核（CPU0）执行 `smp_init()`：
1.  主核遍历 DTB 中的 CPU 节点。
2.  通过 **PSCI**（ARM Trusted Firmware / BL31 提供）执行 `CPU_ON` SMC 调用。
3.  从核从 `secondary_entry`（`head.S` 中的 `secondary_startup`）启动。
4.  从核执行 `secondary_start_kernel()`，初始化自身的 MMU、栈、GIC，最终加入调度器。
5.  **big.LITTLE 拓扑**：DTB 中的 `capacity-dmips-mhz` 属性被解析，内核据此设置 EAS（Energy Aware Scheduling）或 HMP 调度策略，区分 A76 大核与 A55 小核的算力。

## 5. 根文件系统挂载与用户空间启动

1.  **Initramfs / Initrd**：
    *   若存在，内核解压并挂载为临时根文件系统（`rootfs`）。
    *   执行 `/init`（BusyBox 或 Systemd 早期阶段）。
2.  **真实根文件系统挂载**：
    *   根据 `bootargs` 中的 `root=PARTUUID=...` 或 `root=/dev/mmcblk0pX`。
    *   常见参数：`rootwait`（等待设备就绪）、`rootfstype=ext4`、`rw`。
    *   执行 `switch_root` 或 `pivot_root` 切换到真实根。
3.  **执行 PID 1（`/sbin/init`）**：
    *   现代发行版（Ubuntu/Debian）启动 **systemd**。
    *   systemd 按依赖关系启动 `local-fs.target`、`network.target`、`getty.target` 等。
    *   所有内核态驱动完成 `probe`，用户空间服务就绪，出现登录提示符。

## 6. RK3588 典型内存布局与调试特征

*   **内存布局**：
    *   Kernel Image：`0x02080000`
    *   DTB：`0x0a100000` (通常由 Rockchip 的 `resource.img` 或 U-Boot 决定)
    *   Initrd：紧随其后
*   **典型日志**：
    ```text
    [    0.000000] Booting Linux on physical CPU 0x0000000000 [0x412fd050]
    [    0.000000] Machine model: Rockchip RK3588 ...
    [    0.000000] earlycon: uart8250 at MMIO32 0xfeb50000 ...
    [    0.xxx] smp: Bringing up secondary CPUs ...
    [    1.xxx] Run /init as init process
    ```
*   **驱动调试技巧**：
    *   若系统卡死，检查是否在 `EPROBE_DEFER` 循环中（通常是因为某个基础驱动如 Clk 或 Pinctrl 的 DTB 节点缺失或配置错误）。
    *   使用 `initcall_debug` 内核参数，可以打印出每个驱动的 `probe` 耗时，用于优化启动速度（RK3588 优化开机时间的关键手段）。

## 7. 流程简图（专业版）

```text
U-Boot (booti, x0=DTB)
    ↓
primary_entry (head.S)
    ├── el2_setup (降级到 EL1)
    ├── 创建页表, 开启 MMU
    └── __primary_switched → 跳转 C 入口
    ↓
start_kernel()
    ├── setup_arch() → 解析 DTB, 初始化 memblock
    ├── mm_init() / sched_init() / init_IRQ()
    ├── driver_init() → 初始化设备模型
    └── rest_init()
         ├── kernel_thread(kernel_init) → PID 1
         └── kernel_thread(kthreadd) → PID 2
    ↓
kernel_init()
    ├── do_initcalls() → 按优先级加载驱动 (Clk → Pinctrl → PMU → PCIe/NPU/GPU)
    │    └── 匹配 DTB 与 driver → probe() (可能触发 EPROBE_DEFER)
    ├── smp_init() → PSCI 唤醒 A76/A55 从核
    ├── prepare_namespace() → 挂载真实根文件系统
    └── run_init_process("/sbin/init") → execve systemd
    ↓
用户空间 (Systemd) → 系统完全启动
```

此流程在 RK3588 上与标准 ARM64 Linux 保持高度一致，核心差异在于 **PSCI 多核唤醒时序**、**Rockchip 专属时钟/电源/IOMUX 驱动树的解析**，以及**大量使用 `EPROBE_DEFER` 解决驱动依赖乱序问题**。