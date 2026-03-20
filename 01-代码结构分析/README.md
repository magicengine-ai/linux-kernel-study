# 01-代码结构分析 - 学习资料

## 📊 内核架构图

### 整体架构

```mermaid
graph TB
    subgraph 用户空间
        A[应用程序] --> B[系统调用]
    end
    
    subgraph 内核空间
        B --> C[系统调用接口]
        C --> D[子系统层]
        
        subgraph 子系统
            D --> D1[进程调度]
            D --> D2[内存管理]
            D --> D3[文件系统]
            D --> D4[网络设备]
            D --> D5[设备驱动]
        end
        
        D1 --> E[硬件抽象层]
        D2 --> E
        D3 --> E
        D4 --> E
        D5 --> E
        
        E --> F[硬件层]
    end
    
    style A fill:#e1f5ff
    style F fill:#fff4e1
```

### 目录结构层次

```mermaid
mindmap
  root((Linux Kernel))
    arch
      x86
      ARM
      ARM64
      RISC-V
    drivers
      GPU
      Net
      USB
      PCI
    fs
      ext4
      btrfs
      xfs
      nfs
    kernel
      sched
      mm
      irq
      time
    net
      ipv4
      ipv6
      tcp
      wireless
    include
      linux
      uapi
      asm
```

## 📁 目录说明

| 目录 | 作用 | 核心文件 |
|------|------|----------|
| `arch/` | 架构相关代码 | `arch/x86/kernel/` |
| `drivers/` | 设备驱动 | 147 个子系统 |
| `fs/` | 文件系统 | 79 种文件系统 |
| `kernel/` | 核心内核 | 调度、中断等 |
| `mm/` | 内存管理 | 页面分配、VM |
| `net/` | 网络协议栈 | TCP/IP 实现 |
| `include/` | 头文件 | 内核 API 定义 |

## 🔍 代码统计

```mermaid
pie title 代码分布
    "Drivers" : 35
    "Architecture" : 20
    "Filesystems" : 15
    "Networking" : 12
    "Core Kernel" : 10
    "Other" : 8
```

## 📝 学习笔记

### 关键点

1. **分离架构代码** - `arch/` 目录隔离不同 CPU 架构
2. **驱动占比最大** - 硬件支持代码占 35%+
3. **VFS 抽象** - 文件系统通过 VFS 统一接口
4. **模块化设计** - 各子系统相对独立

### 学习建议

1. 从 `kernel/` 核心代码入手
2. 理解 VFS 抽象层设计
3. 掌握设备驱动模型
4. 阅读网络协议栈实现
