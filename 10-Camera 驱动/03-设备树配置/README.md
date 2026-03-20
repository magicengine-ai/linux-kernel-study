# Camera 设备树配置指南

> 完整的硬件描述配置

---

## 📋 设备树基础

设备树用于描述 Camera 硬件连接关系和配置参数。

---

## 🔧 Sensor 节点配置

### OV5640 示例

```dts
&i2c3 {
    clock-frequency = <400000>;
    status = "okay";
    
    ov5640: camera@3c {
        compatible = "ovti,ov5640";
        reg = <0x3c>;
        
        // 时钟输入
        clocks = <&clk_camera>;
        clock-names = "xvclk";
        
        // 电源
        DOVDD-supply = <&reg_1v8>;
        AVDD-supply = <&reg_2v8>;
        DVDD-supply = <&reg_1v2>;
        
        // GPIO 控制
        powerdown-gpios = <&gpio1 5 GPIO_ACTIVE_HIGH>;
        reset-gpios = <&gpio1 6 GPIO_ACTIVE_LOW>;
        
        // MIPI CSI-2 端点
        port {
            ov5640_to_csi2: endpoint {
                remote-endpoint = <&csi2_to_ov5640>;
                clock-lanes = <0>;
                data-lanes = <1 2>;
                link-frequencies = /bits/ 64 <400000000>;
            };
        };
    };
};
```

### IMX219 示例

```dts
&i2c7 {
    status = "okay";
    
    imx219: camera@10 {
        compatible = "sony,imx219";
        reg = <0x10>;
        
        clocks = <&clk_24m>;
        clock-names = "xclk";
        
        avdd-supply = <&reg_2v8>;
        dvdd-supply = <&reg_1v2>;
        dovdd-supply = <&reg_1v8>;
        
        reset-gpios = <&gpio2 3 GPIO_ACTIVE_LOW>;
        standby-gpios = <&gpio2 4 GPIO_ACTIVE_HIGH>;
        
        port {
            imx219_csi_out: endpoint {
                remote-endpoint = <&csi_in>;
                data-lanes = <1 2>;
                link-frequencies = /bits/ 64 <456000000>;
            };
        };
    };
};
```

---

## 🔌 CSI 控制器配置

### MIPI CSI-2

```dts
&csi2 {
    status = "okay";
    
    pinctrl-0 = <&csi2_pins>;
    pinctrl-names = "default";
    
    port {
        csi2_in: endpoint@0 {
            reg = <0>;
            remote-endpoint = <&ov5640_to_csi2>;
        };
        
        csi2_out: endpoint@1 {
            reg = <1>;
            remote-endpoint = <&isp_in>;
        };
    };
};
```

### 并行 DCMI 接口

```dts
&dcmi {
    pinctrl-0 = <&dcmi_pins>;
    pinctrl-names = "default";
    status = "okay";
    
    port {
        dcmi_in: endpoint {
            remote-endpoint = <&sensor_out>;
            bus-width = <8>;
            hsync-active = <1>;          // 高有效
            vsync-active = <1>;          // 高有效
            pixelclk-active = <1>;       // 上升沿采样
            data-shift = <0>;            // 无位移
        };
    };
};
```

---

## 📊 完整系统示例

### STM32 + OV5640

```dts
/dts-v1/;
#include "stm32f429.dtsi"

/ {
    model = "STM32F429 Camera Board";
    compatible = "st,stm32f429-camera";
    
    // 固定稳压器
    reg_1v8: regulator-1v8 {
        compatible = "regulator-fixed";
        regulator-name = "1v8";
        regulator-min-microvolt = <1800000>;
        regulator-max-microvolt = <1800000>;
    };
    
    reg_2v8: regulator-2v8 {
        compatible = "regulator-fixed";
        regulator-name = "2v8";
        regulator-min-microvolt = <2800000>;
        regulator-max-microvolt = <2800000>;
    };
    
    // 摄像头时钟
    clk_camera: camera-clock {
        compatible = "fixed-clock";
        #clock-cells = <0>;
        clock-frequency = <24000000>;
    };
};

&i2c3 {
    status = "okay";
    
    ov5640: camera@3c {
        compatible = "ovti,ov5640";
        reg = <0x3c>;
        
        clocks = <&clk_camera>;
        clock-names = "xvclk";
        
        DOVDD-supply = <&reg_1v8>;
        AVDD-supply = <&reg_2v8>;
        
        powerdown-gpios = <&gpio1 5 GPIO_ACTIVE_HIGH>;
        reset-gpios = <&gpio1 6 GPIO_ACTIVE_LOW>;
        
        port {
            ov5640_p: endpoint {
                remote-endpoint = <&dcmi_ep>;
                bus-width = <8>;
                hsync-active = <1>;
                vsync-active = <1>;
                pixelclk-active = <1>;
            };
        };
    };
};

&dcmi {
    status = "okay";
    
    port {
        dcmi_ep: endpoint {
            remote-endpoint = <&ov5640_p>;
            bus-width = <8>;
            hsync-active = <1>;
            vsync-active = <1>;
            pixelclk-active = <1>;
        };
    };
};
```

---

## 🎯 关键属性说明

### 通用属性

| 属性 | 类型 | 说明 | 示例 |
|------|------|------|------|
| compatible | string | 设备兼容字符串 | "ovti,ov5640" |
| reg | u32 | I2C 地址 | <0x3c> |
| clocks | phandle | 时钟引用 | <&clk_camera> |
| gpio | phandle | GPIO 控制 | <&gpio1 5 GPIO_ACTIVE_HIGH> |

### 电源属性

| 属性 | 说明 |
|------|------|
| AVDD-supply | 模拟电源 |
| DVDD-supply | 数字电源 |
| DOVDD-supply | 数字 I/O 电源 |
| VDDIO-supply | I/O 电源 |

### MIPI CSI-2 属性

| 属性 | 说明 | 示例 |
|------|------|------|
| clock-lanes | 时钟通道 | <0> |
| data-lanes | 数据通道 | <1 2> |
| link-frequencies | 链路频率 | <400000000> |

### 并行接口属性

| 属性 | 说明 | 值 |
|------|------|------|
| bus-width | 数据位宽 | <8> |
| hsync-active | 行同步极性 | <1> 高有效 |
| vsync-active | 场同步极性 | <1> 高有效 |
| pixelclk-active | 像素时钟极性 | <1> 上升沿 |
| data-shift | 数据位移 | <0> 无位移 |

---

## 📝 调试技巧

### 验证设备树

```bash
# 编译设备树
dtc -I dts -O dtb -o stm32f4.dtb stm32f4.dts

# 反编译检查
dtc -I dtb -O dts -o - stm32f4.dtb | less

# 查看编译后的设备树
cat /proc/device-tree/i2c3/camera@3c/compatible
```

### 常见问题

1. **GPIO 不工作** - 检查 GPIO 控制器是否启用
2. **时钟缺失** - 检查时钟树配置
3. **I2C 通信失败** - 检查上拉电阻和地址
4. **电源时序** - 检查上电顺序

---

## ✅ 总结

设备树配置要点：

1. **Sensor 节点** - 兼容字符串、I2C 地址、电源、GPIO
2. **时钟配置** - 外部晶振或内部时钟
3. **接口类型** - MIPI CSI-2 或并行 DCMI
4. **端点连接** - remote-endpoint 链接
5. **总线参数** - 位宽、同步极性

正确的设备树是驱动工作的基础！

---

*学习笔记由 全栈工程师 维护*
