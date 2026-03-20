# 07-文件系统 - 学习资料

## 📊 文件系统架构

### VFS 层次结构

```mermaid
graph TB
    subgraph 用户空间
        A[应用程序] --> B[open/read/write]
    end
    
    subgraph 系统调用层
        B --> C[sys_open]
    end
    
    subgraph VFS 层
        C --> D[struct file]
        D --> E[struct inode]
        E --> F[struct dentry]
    end
    
    subgraph 文件系统层
        F --> G[ext4]
        F --> H[btrfs]
        F --> I[xfs]
    end
    
    subgraph 块设备层
        G --> J[块设备]
        H --> J
        I --> J
    end
    
    style A fill:#e3f2fd
    style J fill:#fff3e0
```

### 文件操作流程

```mermaid
sequenceDiagram
    participant App as 应用程序
    participant VFS as VFS 层
    participant FS as 文件系统
    participant Block as 块设备
    
    App->>VFS: open("file.txt")
    VFS->>FS: lookup inode
    FS-->>VFS: inode
    VFS->>FS: fops->open()
    FS-->>VFS: file descriptor
    VFS-->>App: fd
    
    App->>VFS: read(fd, buf)
    VFS->>FS: fops->read()
    FS->>Block: 读取数据块
    Block-->>FS: 数据
    FS-->>VFS: 数据
    VFS-->>App: 数据
```

### 页面缓存机制

```mermaid
flowchart TD
    A[文件读请求] --> B{页面在缓存？}
    B -->|是 | C[返回缓存页面]
    B -->|否 | D[分配页面]
    D --> E[从磁盘读取]
    E --> F[插入页面缓存]
    F --> C
    
    G[文件写请求] --> H{页面在缓存？}
    H -->|是 | I[修改页面]
    H -->|否 | J[分配页面]
    J --> I
    I --> K[标记为脏页]
    K --> L[后台回写]
    
    style C fill:#c8e6c9
    style L fill:#ffcc80
```

## 📊 文件系统对比

| 文件系统 | 类型 | 最大容量 | 日志 |
|----------|------|----------|------|
| ext4 | 日志式 | 1EB | 是 |
| btrfs | CoW | 16EB | 是 |
| xfs | 日志式 | 8EB | 是 |
| nfs | 网络 | 依赖服务端 | 可选 |

## 🔧 文件系统工具

```bash
# 查看挂载
mount
df -h

# 检查文件系统
fsck /dev/sda1

# 查看 inode
df -i

# EXT4 信息
tune2fs -l /dev/sda1
```

## 📝 学习笔记

### VFS 核心结构

```c
struct super_block { }  // 超级块
struct inode { }        // inode
struct dentry { }       // 目录项
struct file { }         // 文件对象
```

### 文件操作

```c
struct file_operations {
    .open = my_open,
    .read = my_read,
    .write = my_write,
    .release = my_release,
    // ...
};
```

### 性能优化

1. **页面缓存** - 减少磁盘 I/O
2. **预读** - 顺序读优化
3. **延迟分配** - 减少碎片
4. **日志** - 快速恢复
