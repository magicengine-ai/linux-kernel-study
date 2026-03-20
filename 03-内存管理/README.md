# 03-内存管理 - 学习资料

## 📊 内存管理架构

### 内存管理层次

```mermaid
graph TB
    subgraph 虚拟内存
        A[用户进程 VM] --> B[mm_struct]
        B --> C[vm_area_struct]
    end
    
    subgraph 页面缓存
        C --> D[address_space]
        D --> E[page_cache]
    end
    
    subgraph 物理内存
        E --> F[页面分配器]
        F --> G[SLUB 分配器]
        G --> H[物理页面]
    end
    
    subgraph 回收机制
        H --> I[kswapd]
        I --> J[页面换出]
    end
    
    style A fill:#e3f2fd
    style H fill:#fff3e0
```

### 页面分配流程

```mermaid
sequenceDiagram
    participant App as 应用程序
    participant Kernel as 内核
    participant Allocator as 分配器
    participant Page as 页面
    
    App->>Kernel: kmalloc(size)
    Kernel->>Allocator: 请求页面
    Allocator->>Page: 分配物理页
    Page-->>Allocator: 返回页面
    Allocator-->>Kernel: 返回指针
    Kernel-->>App: 返回内存地址
```

### SLUB 分配器架构

```mermaid
mindmap
  root((SLUB))
    Kmem Cache
      per-CPU Slabs
      Partial Slabs
      Full Slabs
    Objects
      固定大小
      快速分配
    优势
      低开销
      简单高效
      碎片少
```

## 📁 内存区域

| Zone | 范围 | 用途 |
|------|------|------|
| ZONE_DMA | 0-16MB | DMA 设备 |
| ZONE_DMA32 | 16MB-4GB | 32 位设备 |
| ZONE_NORMAL | 4GB 以内 | 内核直接映射 |
| ZONE_HIGHMEM | 4GB 以上 | 高端内存 |

## 🔧 调试工具

```bash
# 查看内存信息
cat /proc/meminfo

# 查看页面统计
cat /proc/vmstat

# 查看 slab 使用
slabtop

# 查看 buddy 信息
cat /proc/buddyinfo
```

## 📝 学习笔记

### 关键数据结构

```c
struct page { }           // 页面描述符
struct zone { }           // 内存区域
struct pglist_data { }    // 节点数据
struct kmem_cache { }     // SLUB 缓存
```

### 常见问题

1. **内存泄漏** - 使用 KASAN 检测
2. **碎片化** - 定期整理或使用 CMA
3. **OOM** - 调整 oom_score_adj
4. **性能** - 使用大页 (HugeTLB)
