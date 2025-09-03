# 需求
开启SPI驱动
# 环境
OK

# 1. 设备树关闭原io复用的SAI1端口

> 写入设备树，（注意要写到能够覆盖之前的属性的位置）

简单，disable掉sai1以及依赖sai1的音频芯片esm8288
```dts title:"disable SAI1.dts"
// SPI
// disable SAI1 and SAI1 dependence
&sai1{
  status = "disabled";
};

&es8388_sound {
  status = "disabled";
};
&i2c2{
  es8388{
  status = "disabled";
  };
};
```
# 2. 设备树开启io复用为SPI1
  
> 写入设备树，（注意要写到能够覆盖之前的属性的位置）

```c title:spi1
// cs-gpio, use gpio to cs
&pinctrl{
  spi1_cs0{
    spi1_cs0_gpio_pins: spi1_cs0_gpio_pins{
      rockchip,pins =
        // cs0-gpio
        <0 RK_PB4 RK_FUNC_GPIO &pcfg_pull_none_drv_level_3>;
    };
  };
};

&spi1 {
  status = "okay";
  num-cs = <1>;
  pinctrl-0 = <&spi1_cs0_gpio_pins &spi1_clk_pins>;
  cs-gpios = <&gpio0 RK_PB4 GPIO_ACTIVE_LOW>;
  spi_pll: spi_pll@0{
    status = "okay";
    compatible = "rockchip,spidev";
    reg = <0>;
    spi-max-frequency = <24000000>;
  };
};
```



# 3. spidev的开启

> 直接写入内核编译时对应的deconfig

```config  title:deconfig
// rk3506未开启spidev
// 支持spidev，开启支持
CONFIG_SPI_SPIDEV=y
CONFIG_SPI_BITBANG=y
CONFIG_SPI_ROCKCHIP=y
```
# 4. spidev_test工具的编译

> 工具源代码在kernel里，使用交叉编译工具编译即可

// 编译spidev_test工具
```sh title:compile.sh
forlinx@ubuntu:~/work/OK3506_Linux_Source/kernel/tools/spi$ 
make 
CC=/home/forlinx/work/toochain/arm-buildroot-linux-gnueabihf_sdk-buildroot/bin/arm-buildroot-linux-gnueabihf-gcc 
LD=/home/forlinx/work/toochain/arm-buildroot-linux-gnueabihf_sdk-buildroot/bin/arm-buildroot-linux-gnueabihf-ld

forlinx@ubuntu:~/work/OK3506_Linux_Source/kernel/tools/spi$ 
file spidev_test
spidev_test: ELF 32-bit LSB pie executable, ARM, EABI5 version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-armhf.so.3, for GNU/Linux 3.2.0, with debug_info, not stripped
```


# 5.spidev_test工具的使用

```sh title:test.sh
spidev_test -D /dev/spidev1.0 -s 1000000
spidev_test -D /dev/spidev1.0 -s 1000000 -b 8 -d 1000 -H -p 'hello'
```

硬件线序不对

![[PixPin_2025-08-26_14-24-24.webp]]