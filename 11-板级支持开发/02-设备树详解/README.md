# 设备树详解

> 硬件描述语言完整指南

---

## 📋 设备树基础

设备树 (Device Tree) 是描述硬件的数据结构，用于分离代码和硬件配置。

---

## 🌳 语法基础

### 节点定义

```dts
/ {
    compatible = "vendor,soc-name";
    #address-cells = <1>;
    #size-cells = <1>;
    
    // 子节点
    node@address {
        compatible = "vendor,device";
        reg = <0x10000000 0x1000>;
        interrupts = <0 64 4>;
    };
};
```

### 属性类型

```dts
// 整数
property = <0x1234>;

// 字符串
property = "value";

// 字节数组
property = [0x12 0x34 0x56];

// 数组
property = <1 2 3 4>;

// 引用
property = <&node_label>;
```

---

## 🔧 常用属性

| 属性 | 说明 | 示例 |
|------|------|------|
| compatible | 兼容字符串 | "st,stm32f429" |
| reg | 寄存器地址 | <0x40020000 0x400> |
| interrupts | 中断号 | <0 64 4> |
| clocks | 时钟引用 | <&clk_hse> |
| gpios | GPIO 引用 | <&gpio1 5 GPIO_ACTIVE_HIGH> |
| status | 设备状态 | "okay" / "disabled" |

---

## 📊 设备树编译

```bash
# 编译 DTS 到 DTB
dtc -I dts -O dtb -o stm32f4.dtb stm32f4.dts

# 反编译 DTB 到 DTS
dtc -I dtb -O dts -o stm32f4.dts stm32f4.dtb

# 查看编译后的设备树
cat /proc/device-tree/compatible
```

---

## ✅ 总结

设备树核心要点：

1. **节点** - 硬件设备描述
2. **属性** - 设备参数配置
3. **引用** - 设备间关联
4. **编译** - DTS ↔ DTB

---

*学习笔记由 全栈工程师 维护*
