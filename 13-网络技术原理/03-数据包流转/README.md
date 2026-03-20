# 网络数据包流转

> 从网卡到应用的完整路径

---

## 📋 数据包接收流程

```mermaid
flowchart TD
    A[网卡接收] --> B[DMA 到内存]
    B --> C[硬件中断]
    C --> D[NAPI 轮询]
    D --> E[构建 sk_buff]
    E --> F[协议栈处理]
    F --> G{类型判断}
    G -->|TCP| H[TCP 层]
    G -->|UDP| I[UDP 层]
    H --> J[套接字层]
    I --> J
    J --> K[用户空间]
    
    style A fill:#ffcdd2
    style K fill:#c8e6c9
```

---

## 🔧 NAPI 机制

### 传统中断方式问题

```mermaid
sequenceDiagram
    participant NIC as 网卡
    participant CPU as CPU
    participant Kernel as 内核
    
    NIC->>CPU: 中断请求
    CPU->>Kernel: 处理中断
    Kernel->>NIC: 读取数据包
    NIC->>CPU: 中断请求
    CPU->>Kernel: 处理中断
    Kernel->>NIC: 读取数据包
    
    Note over CPU: 高负载下中断风暴
```

### NAPI 混合方式

```mermaid
sequenceDiagram
    participant NIC as 网卡
    participant CPU as CPU
    participant Kernel as 内核
    
    NIC->>CPU: 第一次中断
    CPU->>Kernel: 禁用中断
    Kernel->>NIC: 轮询模式
    loop 批量处理
        Kernel->>NIC: 读取多个数据包
    end
    Kernel->>CPU: 启用中断
```

---

## 📊 数据包发送流程

```mermaid
flowchart LR
    A[应用层] --> B[send 系统调用]
    B --> C[构建 sk_buff]
    C --> D[TCP 层处理]
    D --> E[IP 层处理]
    E --> F[路由查找]
    F --> G[ARP 解析]
    G --> H[网卡驱动]
    H --> I[DMA 传输]
    I --> J[物理网卡]
    
    style A fill:#e3f2fd
    style J fill:#fff3e0
```

---

## 💻 实践示例

### 抓包分析

```bash
# 使用 tcpdump 抓包
tcpdump -i eth0 -n port 80

# 使用 wireshark 分析
wireshark capture.pcap

# 内核跟踪
trace-cmd record -e net:*
trace-cmd report
```

### 网络统计

```bash
# 查看网络统计
cat /proc/net/dev

# 查看 TCP 统计
netstat -s

# 查看连接状态
ss -tuln
```

---

## ✅ 总结

数据包流转核心：

1. **接收** - 中断→NAPI→协议栈
2. **发送** - 应用→协议栈→DMA
3. **NAPI** - 中断 + 轮询
4. **sk_buff** - 统一缓冲

---

*学习笔记由 全栈工程师 维护*
