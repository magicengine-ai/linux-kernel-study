# 板级开发实战案例

> STM32/树莓派/i.MX 完整移植示例

---

## 📋 案例概述

本章通过实际案例演示完整的板级移植流程。

---

## 🎯 案例 1: STM32F429 移植

### 硬件规格

| 参数 | 规格 |
|------|------|
| SoC | STM32F429ZIT6 |
| CPU | ARM Cortex-M4 @ 180MHz |
| 内存 | 2MB Flash, 256KB SRAM |
| 外设 | LCD, Camera, Ethernet |

### 移植步骤

#### 1. 创建机器目录

```bash
mkdir -p arch/arm/mach-stm32
```

#### 2. 编写 Kconfig

```kconfig
config ARCH_STM32
    bool "STM32 Platforms"
    depends on ARCH_MULTI_V7
    select ARM_GIC
    select COMMON_CLK

config MACH_STM32F429
    bool "STM32F429 SoC"
    select MACH_STM32F4
```

#### 3. 创建设备树

```dts
// stm32f429-disco.dts
/dts-v1/;
#include "stm32f429.dtsi"

/ {
    model = "ST STM32F429 Discovery";
    compatible = "st,stm32f429-disco", "st,stm32f429";
    
    memory {
        device_type = "memory";
        reg = <0x08000000 0x00200000>;
    };
};
```

#### 4. 编译测试

```bash
make ARCH=arm CROSS_COMPILE=arm-none-eabi- stm32f429_defconfig
make ARCH=arm CROSS_COMPILE=arm-none-eabi- -j$(nproc)
make ARCH=arm CROSS_COMPILE=arm-none-eabi- stm32f429-disco.dtb
```

---

## 🎯 案例 2: 树莓派 CM4 移植

### 硬件规格

| 参数 | 规格 |
|------|------|
| SoC | Broadcom BCM2711 |
| CPU | Quad-core Cortex-A72 @ 1.5GHz |
| 内存 | 2GB/4GB/8GB LPDDR4 |
| 存储 | eMMC / microSD |

### 关键配置

```dts
// bcm2711-rpi-cm4.dts
/dts-v1/;
#include "bcm2711.dtsi"

/ {
    model = "Raspberry Pi Compute Module 4";
    compatible = "raspberrypi,cm4", "brcm,bcm2711";
    
    // 内存配置
    memory@0 {
        device_type = "memory";
        reg = <0x0 0x0 0x0 0x80000000>;  // 2GB
    };
};
```

---

## 🎯 案例 3: i.MX6ULL 移植

### 硬件规格

| 参数 | 规格 |
|------|------|
| SoC | NXP i.MX6ULL |
| CPU | ARM Cortex-A7 @ 900MHz |
| 内存 | 512MB DDR3 |
| 存储 | 8GB eMMC |

### 设备树配置

```dts
// imx6ull-14x14-evk.dts
/dts-v1/;
#include "imx6ull.dtsi"

/ {
    model = "NXP i.MX6ULL 14x14 EVK";
    compatible = "fsl,imx6ull-14x14-evk", "fsl,imx6ull";
    
    memory@80000000 {
        device_type = "memory";
        reg = <0x80000000 0x20000000>;  // 512MB
    };
};

&uart1 {
    pinctrl-names = "default";
    pinctrl-0 = <&pinctrl_uart1>;
    status = "okay";
};
```

---

## 📝 移植检查清单

### 必须完成项

- [ ] SoC 支持代码
- [ ] 设备树编写
- [ ] 时钟驱动
- [ ] GPIO 驱动
- [ ] 串口驱动
- [ ] 中断配置
- [ ] defconfig 配置

### 可选完成项

- [ ] 以太网驱动
- [ ] USB 驱动
- [ ] SDIO/MMC驱动
- [ ] LCD 驱动
- [ ] Camera 驱动
- [ ] 音频驱动

---

## ✅ 总结

板级移植核心流程：

1. **SoC 支持** - 芯片级代码
2. **设备树** - 硬件描述
3. **驱动移植** - 外设支持
4. **编译测试** - 功能验证
5. **调试优化** - 问题解决

---

*学习笔记由 全栈工程师 维护*
