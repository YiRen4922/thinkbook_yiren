- **SRAM 初始化（已勾选）**：正确（也可以理解为 BootROM 已经完成，TPL/SPL 也是运行在 SRAM 中）。
    
- **DDR/LPDDR 初始化（已勾选）**：正确。这是 TPL/SPL 阶段最核心的任务。由于 BootROM 太小放不下复杂的 DDR 训练代码，所以 BootROM 会把 TPL（Tiny Program Loader）加载到 SRAM 中运行。TPL 负责初始化 DDR 内存，有了 DDR 之后，才能把完整的 U-Boot 加载到 DDR 中运行。
    
- 注：在 Rockchip 的官方 SDK 中，这个阶段经常被称为 `miniloader`（基于 Rockchip 专有代码）或者主线 U-Boot 下的 `TPL+SPL`。


在RK3588的启动流程中，**TPL（或miniloader）是承接BootROM、启动DDR内存的关键阶段**，它为后续加载完整的U-Boot和操作系统内核铺平了道路。

### 🧩 两套并行的启动方案

Rockchip平台目前根据前级Loader（预加载器）的代码是否开源，提供了两套启动方案：

*   **方案一（闭源厂商方案）：** `BootROM -> ddr.bin -> miniloader -> Trust -> U-Boot -> Kernel`。此方案使用Rockchip官方提供的闭源二进制文件。
*   **方案二（开源主线方案）：** `BootROM -> TPL -> SPL -> Trust -> U-Boot -> Kernel`。此方案使用U-Boot开源代码编译生成，其中**TPL在功能上相当于闭源的ddr.bin**，**SPL在功能上相当于闭源的miniloader**。

这两套方案功能一致，可以相互替换。对于开发者而言，选择开源方案意味着更高的透明度和可定制性。

### 🏗️ TPL / miniloader的核心职责

无论哪种方案，这一阶段的核心任务都是为系统“铺路”，具体包括：

*   **初始化DDR内存（核心任务）**：由于BootROM未初始化外部DDR，CPU只能运行在容量极小的内部SRAM中。TPL（或ddr.bin）的首要任务就是完成复杂的DDR初始化（包括时序配置、训练等），**让系统首次拥有大容量内存空间**。在RK3588中，TPL还会编码内存信息（如16GB以上DRAM的内存空洞）供后续阶段使用。
*   **初始化存储设备并加载下一阶段**：miniloader（或SPL）负责初始化eMMC、SD卡等启动存储设备，从中读取完整的U-Boot镜像并加载到DDR中，然后跳转执行。

### ⚙️ miniloader的存储类型变体

miniloader作为Rockchip的厂商实现，针对不同的存储介质提供了优化版本：

*   **标准eMMC/SD版本**：最常见的通用版本，用于eMMC或SD卡启动。
*   **SPI NOR版本**：针对SPI NOR Flash优化，支持直接访问，无需FTL，启动更快。
*   **FTL (NAND) 版本**：**专为SPI NAND Flash设计**，提供了关键的**损耗均衡（Wear Leveling）**、**坏块管理**和**地址映射**等功能，以管理NAND闪存的物理特性。

### ⏱️ 启动耗时参考

在RK3588平台上，整个启动流程的耗时大致分布如下：TPL阶段约**50ms**，SPL/miniloader阶段约**200ms**，后续的ATF和U-Boot阶段耗时更长。如果追求极致优化，例如将Linux启动压缩到1秒内，这个阶段的优化是重要环节。