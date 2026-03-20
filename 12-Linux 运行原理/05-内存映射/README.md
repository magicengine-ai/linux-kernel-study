# 内存映射与 I/O

> mmap 和直接内存访问

---

## 📋 内存映射概述

内存映射 (mmap) 将文件或设备映射到进程地址空间。

---

## 🔧 mmap 使用

```c
#include <sys/mman.h>
#include <fcntl.h>
#include <unistd.h>

// 映射文件
void *mmap(void *addr, size_t length, int prot, int flags,
           int fd, off_t offset);

// 取消映射
int munmap(void *addr, size_t length);
```

---

## 💻 mmap 示例

```c
#include <stdio.h>
#include <fcntl.h>
#include <sys/mman.h>
#include <unistd.h>

int main() {
    int fd = open("file.txt", O_RDONLY);
    
    // 获取文件大小
    size_t size = lseek(fd, 0, SEEK_END);
    
    // 内存映射
    char *mapped = mmap(NULL, size, PROT_READ, MAP_PRIVATE, fd, 0);
    
    // 直接访问文件内容
    printf("Content: %s\n", mapped);
    
    // 取消映射
    munmap(mapped, size);
    close(fd);
    
    return 0;
}
```

---

## ✅ 总结

内存映射核心：

1. **mmap** - 文件/设备映射
2. **零拷贝** - 高效 I/O
3. **共享映射** - 进程间通信
4. **直接访问** - 内存级别速度

---

*学习笔记由 全栈工程师 维护*
