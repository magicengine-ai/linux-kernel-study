# 进程通信机制详解

> IPC 完整指南

---

## 📋 IPC 概述

进程通信 (IPC - Inter-Process Communication) 是进程间交换数据的机制。

---

## 🔧 IPC 方式对比

| 方式 | 速度 | 复杂度 | 适用场景 |
|------|------|--------|----------|
| 管道 | ⭐⭐⭐ | 低 | 父子进程 |
| 消息队列 | ⭐⭐ | 中 | 任意进程 |
| 共享内存 | ⭐⭐⭐⭐⭐ | 高 | 大数据量 |
| 信号量 | ⭐⭐⭐ | 中 | 同步控制 |
| 套接字 | ⭐⭐ | 中 | 网络/本地 |

---

## 💻 共享内存示例

```c
#include <sys/ipc.h>
#include <sys/shm.h>
#include <stdio.h>
#include <string.h>

#define SHM_SIZE 1024

int main() {
    key_t key = ftok("shmfile", 65);
    
    // 创建共享内存
    int shmid = shmget(key, SHM_SIZE, 0666|IPC_CREAT);
    
    // 映射到进程空间
    char *str = (char*) shmat(shmid, (void*)0, 0);
    
    // 写入数据
    sprintf(str, "Hello Shared Memory!");
    
    printf("Data written: %s\n", str);
    
    // 分离共享内存
    shmdt(str);
    
    // 销毁共享内存
    shmctl(shmid, IPC_RMID, NULL);
    
    return 0;
}
```

---

## ✅ 总结

IPC 核心要点：

1. **管道** - 简单、单向通信
2. **消息队列** - 结构化数据
3. **共享内存** - 最高性能
4. **信号量** - 同步机制
5. **套接字** - 通用通信

---

*学习笔记由 全栈工程师 维护*
