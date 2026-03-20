# 08-Linux 内核 Rust 编程

> 版本：v7.0-rc4  
> 更新时间：2026-03-20

---

## 📋 概述

Linux 内核 Rust 支持：
- 内存安全驱动开发
- 零成本抽象
- 与 C 代码互操作
- 类型安全保障
- 并发安全保证

---

## 🏗️ Rust 内核架构

```
rust/
├── kernel/             # Rust 内核绑定
│   ├── lib.rs          # 内核库根
│   ├── alloc.rs        # 内存分配
│   ├── error.rs        # 错误处理
│   ├── init.rs         # 初始化框架
│   ├── types.rs        # 类型定义
│   ├── sync.rs         # 同步原语
│   ├── time.rs         # 时间 API
│   ├── device.rs       # 设备抽象
│   ├── driver.rs       # 驱动框架
│   ├── platform.rs     # 平台驱动
│   ├── pci.rs          # PCI 驱动
│   ├── usb.rs          # USB 驱动
│   ├── net.rs          # 网络驱动
│   ├── block.rs        # 块设备驱动
│   └── char.rs         # 字符设备驱动
│
├── bindings/           # C 绑定 (自动生成)
├── helpers/            # Rust 辅助代码
└── macros/             # Rust 宏
```

---

## 📦 环境配置

### 工具链要求

```bash
# Rust 工具链
rustup install stable
rustup default stable
rustup component add rust-src rustfmt clippy

# 内核构建要求
CONFIG_RUST=y
CONFIG_RUST_EXAMPLES=y
CONFIG_SAMPLE_RUST=y
```

### 检查 Rust 支持

```bash
# 检查 Rust 可用性
make rustavailable
```

---

## 🔧 第一个 Rust 内核模块

### 模块模板

```rust
#![no_std]
#![feature(register_module)]

use kernel::prelude::*;

module! {
    type: Hello,
    name: "rust_hello",
    author: "Your Name",
    description: "Rust Hello World Module",
    license: "GPL",
}

struct Hello;

impl kernel::Module for Hello {
    fn init(_name: &'static CStr, _module: &'static ThisModule) -> Result<Self> {
        pr_info!("Rust Hello World!\n");
        Ok(Self {})
    }
}
```

---

## 🔐 内存安全

### 智能指针

```rust
use kernel::sync::{Arc, Mutex};
use kernel::alloc::Box;

// Arc - 引用计数指针
let data = Arc::new(MyData { value: 42 });

// Box - 堆分配
let boxed: Box<i32> = Box::new(42);

// 带锁的数据
let locked_data = Arc::new(Mutex::new(MyData { value: 42 }));
```

---

## 🧵 并发安全

### Mutex

```rust
use kernel::sync::Mutex;

struct Device {
    counter: Mutex<i32>,
}

impl Device {
    fn increment(&self) {
        let mut counter = self.counter.lock();
        *counter += 1;
    }
}
```

### Atomic

```rust
use core::sync::atomic::{AtomicU32, Ordering};

struct AtomicCounter {
    count: AtomicU32,
}

impl AtomicCounter {
    fn increment(&self) {
        self.count.fetch_add(1, Ordering::Relaxed);
    }
}
```

---

## 🚫 错误处理

### Result 类型

```rust
use kernel::error::{Error, Result};

fn may_fail(value: i32) -> Result<i32> {
    if value < 0 {
        return Err(Error::EINVAL);
    }
    Ok(value * 2)
}

fn process() -> Result<()> {
    let result = may_fail(5)?;
    Ok(())
}
```

---

## 🔌 设备驱动框架

### 字符设备驱动

```rust
use kernel::chardev::{self, Operations, File};

struct RustCharDev {
    _chardev_registration: chardev::Registration,
}

impl Operations for OperationsImpl {
    type Data = i32;
    
    fn read(&self, _file: &File, data: &mut i32, 
            buf: &mut [u8], _offset: u64) -> Result<usize> {
        // 读取实现
    }
}
```

### 平台驱动

```rust
use kernel::platform::{PlatformDriver, PlatformDevice};

impl PlatformDriver for RustDriver {
    fn probe(&self, device: &PlatformDevice) -> Result<Self> {
        pr_info!("Probing Rust platform driver\n");
        Ok(Self {})
    }
    
    fn remove(&self, _device: &PlatformDevice) {
        pr_info!("Removing Rust platform driver\n");
    }
}
```

---

## 🔔 中断处理

```rust
use kernel::irq::{Irq, IrqFlags};

let irq = Irq::new(
    irq_num,
    IrqFlags::TRIGGER_RISING,
    "rust-device",
    Self::irq_handler,
)?;

fn irq_handler(irq: u32, dev_id: &mut ()) -> kernel::irq::IrqReturn {
    kernel::irq::IrqReturn::Handled
}
```

---

## 🧪 调试技巧

### 打印宏

```rust
pr_info!("Info message\n");
pr_warn!("Warning message\n");
pr_err!("Error message\n");
pr_debug!("Debug message\n");
```

---

## 📚 参考资源

| 资源 | 链接 |
|------|------|
| Rust 内核文档 | Documentation/rust/ |
| Rust for Linux | https://rust-for-linux.com/ |
| 内核 Rust API | https://rust.docs.kernel.org/ |

---

## ✅ 总结

Linux 内核 Rust 编程核心要点：

- **内存安全** - 无裸指针，智能指针管理
- **类型安全** - 编译时检查
- **并发安全** - Mutex、SpinLock、Atomic
- **错误处理** - Result 类型，? 操作符
- **驱动框架** - 字符设备、平台驱动

Rust 是内核驱动开发的未来！

---

*学习笔记由 全栈工程师 维护*
