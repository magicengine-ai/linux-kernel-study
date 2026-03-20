# Linux 内核架构

> 内核空间与用户空间

---

## 📋 内核架构概述

```mermaid
graph TB
    subgraph 用户空间
        A[应用程序] --> B[系统调用接口]
    end
    
    subgraph 内核空间
        B --> C[系统调用层]
        C --> D[内核子系统]
        D --> E[硬件抽象层]
    end
    
    E --> F[硬件层]
    
    style A fill:#e3f2fd
    style F fill:#fff3e0
```

---

## 🏗️ 内核空间 vs 用户空间

| 特性 | 用户空间 | 内核空间 |
|------|----------|----------|
| 地址范围 | 0x00000000-0x7FFFFFFF | 0x80000000-0xFFFFFFFF |
| 权限 | 受限 (Ring 3) | 完全 (Ring 0) |
| 访问硬件 | 不可直接访问 | 可直接访问 |
| 稳定性 | 崩溃不影响系统 | 崩溃导致系统宕机 |

---

## 🔧 内核子系统

```mermaid
mindmap
  root((内核))
    进程管理
      调度器
      进程控制
    内存管理
      页面分配
      虚拟内存
    文件系统
      VFS
      具体 FS
    设备驱动
      字符设备
      块设备
      网络设备
    网络协议
      TCP/IP
      套接字
```

---

## 📊 内存布局

```mermaid
graph TB
    subgraph 用户空间
        A[栈 Stack] --> B[堆 Heap]
        B --> C[共享库]
        C --> D[代码段]
    end
    
    subgraph 内核空间
        E[虚拟内存映射] --> F[内核代码]
        F --> G[内核数据]
    end
    
    style A fill:#e3f2fd
    style E fill:#fff3e0
```

---

## ✅ 总结

Linux 内核架构核心：

1. **双空间** - 用户空间/内核空间隔离
2. **系统调用** - 唯一通信接口
3. **子系统** - 模块化设计
4. **硬件抽象** - 统一接口

---

*学习笔记由 全栈工程师 维护*
