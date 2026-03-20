# 虚拟内存管理

> VMA 和地址空间管理

---

## 📋 概述

虚拟内存管理负责进程地址空间的映射和管理。

---

## 🏗️ VMA 结构

```c
struct vm_area_struct {
    unsigned long vm_start;       // 起始地址
    unsigned long vm_end;         // 结束地址
    struct mm_struct *vm_mm;      // 所属进程
    pgprot_t vm_page_prot;        // 页面保护
    unsigned long vm_flags;       // 标志
    struct rb_node vm_rb;         // 红黑树节点
};
```

---

## ✅ 总结

虚拟内存核心：

1. **VMA** - 虚拟内存区域
2. **mm_struct** - 进程内存描述符
3. **页表** - 地址转换
4. **缺页中断** - 按需分配

---

*学习笔记由 全栈工程师 维护*
