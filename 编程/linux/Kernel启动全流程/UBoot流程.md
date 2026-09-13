**RK3588 U-Boot 全流程详解**（专业级，基于 Rockchip 官方与上游 U-Boot 实现）

RK3588（ARMv8.2-A，4×Cortex-A76 + 4×Cortex-A55）采用典型的多级 Bootloader 架构。启动链严格遵循 **BootROM → TPL/SPL（或 Miniloader）→ ATF (BL31) → U-Boot Proper → Kernel** 的顺序。支持完全开源路径（TPL+SPL）和 Rockchip 闭源路径（DDR bin + Miniloader）。

### 1. 上电与 BootROM（BL1）阶段
- **入口点**：CPU0（大核集群第一个 A76）从上电复位后从物理地址 **0x00000000** 取指执行。该地址硬连线到片内 **Mask ROM**（BootROM）。
- **BootROM 主要工作**：
  1. 初始化最基础的时钟、电源、IOMUX。
  2. 读取启动配置引脚（主要是 `SARADC_IN0_BOOT` 电压分压，参考硬件设计指南），决定启动介质优先级。
  3. 按固定顺序扫描启动介质：
     - SPI NOR / SPI NAND
     - eMMC（通常优先级最高）
     - SDMMC（SD 卡）
  4. 在选定介质的 **扇区 64（0x40，偏移 32 KiB）** 处查找有效的 **ID Block**（Rockchip 专有头部）。
  5. 验证头部校验和后，将第一级 Loader（TPL 或 Miniloader 的 init 部分）加载到 **内部 SRAM** 并跳转执行。
- **SRAM 容量**：PMU_SRAM + SYSTEM_SRAM，总容量约数百 KB（足够运行 TPL）。
- **失败处理**：所有介质均无有效 ID Block 时，进入 **MaskRom 模式**，通过 USB OTG 等待 `rkdeveloptool` / `rockusb` 下载（命令 0x471 加载 TPL 到 SRAM，0x472 加载 SPL 到 DRAM）。

### 2. TPL（Tertiary Program Loader）阶段
- **运行位置**：内部 SRAM。
- **核心任务**：
  - 初始化 **DRAM 控制器**（LPDDR4/LPDDR4X/LPDDR5）。
  - 执行 **DDR Training**（频率、时序、训练参数）。
  - 典型 DDR 二进制示例（rkbin）：
    - `rk3588_ddr_lp4_2112MHz_lp5_2400MHz_v1.xx.bin`（或更高版本）
    - 支持最高频率约 2112 MHz（LPDDR4X）或 2400+ MHz（LPDDR5）。
  - 初始化完成后返回 BootROM（通过 `ROCKCHIP_BACK_TO_BROM` 机制）。
- **输出**：DRAM 可用，BootROM 继续加载下一阶段到 DRAM。

> **注意**：DDR 初始化二进制目前仍为 Rockchip 闭源（上游 U-Boot 使用外部 `ROCKCHIP_TPL`）。开源 TPL 支持有限。

### 3. SPL（Secondary Program Loader）阶段
- **运行位置**：DRAM（通常从低地址开始）。
- **核心任务**：
  - 初始化存储控制器（eMMC / SD / SPI / UFS）。
  - 从存储介质加载后续镜像（通常从扇区 **0x4000**，偏移约 8 MiB 处加载 `u-boot.itb` 或 `uboot.img`）。
  - 加载并验证 **ATF (BL31)** 和 **U-Boot Proper**。
  - 可选加载 OP-TEE（BL32）。
  - 跳转到 BL31。
- **常见镜像组合**：
  - 开源路径：`idbloader.img` = TPL + SPL（通过 `mkimage -n rk3588 -T rksd` 打包）。
  - 闭源路径：`idbloader.img`（DDR bin + Miniloader）。

### 4. ATF / BL31（ARM Trusted Firmware）阶段
- **运行位置**：DRAM（典型加载地址 **0x00040000**）。
- **核心任务**（EL3 最高特权级）：
  - 初始化安全监控器（Secure Monitor）。
  - 配置 **PSCI**（Power State Coordination Interface），用于后续唤醒其他 7 个核心。
  - 设置 TrustZone、防火墙（FIREWALL_DDR、FIREWALL_SYSMEM）。
  - 可选初始化 OP-TEE（BL32，典型地址 0x08400000）。
  - 完成安全世界初始化后，切换到非安全世界（EL2），跳转到 U-Boot Proper（BL33）。
- **上游支持**：Trusted Firmware-A 已支持 RK3588（v2.12+），可使用开源 `bl31.elf`。

### 5. U-Boot Proper（BL33）阶段
- **运行位置**：DRAM 高地址（典型 `TEXT_BASE` 约 0x00A00000 或 8 MiB 偏移以上）。
- **主要初始化流程**（`board_init_f` → `board_init_r`）：
  1. 早期初始化：时钟、串口（debug UART）、timer。
  2. 完整 DRAM bank 配置（通过 ATAGS 或 `rockchip_dram_init_banksize` 解析 TPL 传递的 DDR_MEM 标签）。
     - 支持最大 32 GB DRAM，需处理高地址空洞（如 16+ GB 时存在 0x3FC000000 ~ 0x400000000 等 gap）。
  3. 设备树（FDT）加载与 fixup。
  4. 存储、网络、USB、PCIe 等驱动初始化。
  5. 环境变量加载（`env` 从 eMMC/SD 分区）。
  6. 启动模式检测（`rockchip_get_boot_mode`）：Normal / Recovery / Fastboot 等。
  7. 加载内核镜像（通常 FIT 或 Android boot.img）+ DTB + Ramdisk。
  8. 设置 `bootargs`，跳转到内核入口。

**典型启动命令**（`bootcmd`）：
```bash
boot_fit; boot_android ${devtype} ${devnum}; bootrkp; run distro_bootcmd;
```

**镜像布局示例**（eMMC/SD）：
- 0x40 扇区：`idbloader.img`
- 0x4000 扇区：`u-boot.itb`（含 U-Boot + ATF + FDT）或 `uboot.img` + `trust.img`
- 更高分区：`boot.img`、`rootfs` 等

### 6. 内核启动与后续
- U-Boot 通过 `bootm` / `booti` 将控制权交给 Linux Kernel。
- Kernel 通过 PSCI 调用唤醒其余 CPU 核心。
- 安全世界服务（ATF / OP-TEE）持续运行，处理 SMC 调用。

### 关键地址与偏移汇总（RK3588 典型值）
| 组件              | 典型地址 / 偏移                  | 说明 |
|-------------------|----------------------------------|------|
| BootROM 入口      | 0x00000000                       | Mask ROM |
| ID Block          | 扇区 0x40（32 KiB）              | BootROM 查找 |
| TPL/SPL 加载      | SRAM → DRAM                      | - |
| BL31 (ATF)        | 0x00040000                       | EL3 |
| BL32 (OP-TEE)     | 0x08400000                       | 可选 |
| U-Boot TEXT_BASE  | ≈ 0x00A00000 或更高              | Proper |
| U-Boot 加载位置   | 扇区 0x4000（约 8 MiB）          | 常见 |

### 开源 vs 闭源路径对比
| 路径          | 阶段组成                          | 优点                     | 缺点 |
|---------------|-----------------------------------|--------------------------|------|
| **开源**      | BootROM → TPL → SPL → ATF → U-Boot | 完全可控、上游支持好     | DDR 训练仍依赖闭源 bin |
| **闭源**      | BootROM → DDR bin + Miniloader → TRUST → U-Boot | 官方优化、兼容性最好     | 黑盒、难以调试 |

### 调试要点
- 串口日志阶段清晰：BootROM → Loader（DDR 信息）→ U-Boot 版本与环境 → Kernel。
- 中断自动启动：看到 `Hit any key to stop autoboot` 时按键进入 U-Boot 命令行。
- MaskRom 模式：用 `rkdeveloptool db` 下载 loader，再 `wl` 烧写镜像。

以上流程基于 Rockchip 官方文档、上游 U-Boot（`arch/arm/mach-rockchip`）、rkbin 与社区实践（2024–2026）。具体板级（如 ROCK 5B、NanoPC-T6、Orange Pi 5 等）在 DDR bin 选择、IOMUX、启动优先级上会有细微差异，但整体架构一致。