# 04-进程调度 - 学习资料

## 📊 调度器架构

### CFS 调度器流程

```mermaid
flowchart TD
    A[进程就绪] --> B[计算 vruntime]
    B --> C[插入红黑树]
    C --> D[调度器选择]
    D --> E{选择最左节点}
    E -->|vruntime 最小 | F[执行进程]
    F --> G[更新 vruntime]
    G --> H{时间片用完？}
    H -->|是 | C
    H -->|否 | I[继续执行]
    
    style A fill:#e3f2fd
    style F fill:#c8e6c9
```

### 调度器类层次

```mermaid
graph TB
    subgraph 调度器类
        A[Stop Task] -->|优先级最高 | B[RT Task]
        B --> C[Deadline Task]
        C --> D[CFS Task]
        D --> E[Idle Task]
    end
    
    E -->|优先级最低 | F[CPU 空闲]
    
    style A fill:#ffcdd2
    style E fill:#c8e6c9
```

### 进程状态转换

```mermaid
stateDiagram-v2
    [*] --> RUNNABLE: fork()
    RUNNABLE --> RUNNING: 调度器选择
    RUNNING --> RUNNABLE: 时间片用完
    RUNNING --> SLEEPING: 等待事件
    SLEEPING --> RUNNABLE: 事件到达
    RUNNING --> ZOMBIE: exit()
    ZOMBIE --> [*]: wait()
    
    note right of RUNNING
        正在 CPU 执行
    end note
    
    note right of SLEEPING
        等待 I/O 等事件
    end note
```

## 📊 优先级对比

| 类型 | 范围 | 默认值 |
|------|------|--------|
| RT 优先级 | 0-99 | - |
| Nice 值 | -20 到 19 | 0 |
| 普通优先级 | 100-139 | 120 |

## 🔧 调度调试

```bash
# 查看进程调度信息
cat /proc/[pid]/sched

# 查看调度统计
cat /proc/schedstat

# 实时任务查看
chrt -p [pid]

# 调度延迟分析
perf sched latency
```

## 📝 学习笔记

### CFS 核心概念

1. **vruntime** - 虚拟运行时间，保证公平
2. **红黑树** - 高效选择下一个进程
3. **权重** - Nice 值转换为负载权重
4. **唤醒粒度** - 交互式任务优化

### 调度策略

```c
SCHED_NORMAL    // 普通进程 (CFS)
SCHED_FIFO      // 实时 FIFO
SCHED_RR        // 实时轮转
SCHED_DEADLINE  // 截止时间
SCHED_IDLE      // 空闲优先级
```

### 调优参数

```bash
/proc/sys/kernel/sched_min_granularity_ns
/proc/sys/kernel/sched_latency_ns
/proc/sys/kernel/sched_wakeup_granularity_ns
```
