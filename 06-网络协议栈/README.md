# 06-网络协议栈 - 学习资料

## 📊 网络架构

### OSI 模型对比

```mermaid
graph TB
    subgraph OSI 模型
        A1[7. 应用层]
        A2[6. 表示层]
        A3[5. 会话层]
        A4[4. 传输层]
        A5[3. 网络层]
        A6[2. 数据链路层]
        A7[1. 物理层]
    end
    
    subgraph TCP/IP
        B1[应用层] --> A1
        B2[传输层 TCP/UDP] --> A4
        B3[网络层 IP] --> A5
        B4[链路层] --> A6
        B5[物理层] --> A7
    end
    
    style B2 fill:#e3f2fd
    style B3 fill:#fff3e0
```

### SKB 数据包流转

```mermaid
flowchart LR
    A[网卡接收] --> B[DMA 到内存]
    B --> C[硬件中断]
    C --> D[NAPI 轮询]
    D --> E[构建 SKB]
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

### TCP 状态机

```mermaid
stateDiagram-v2
    [*] --> CLOSED
    
    state server_side {
        CLOSED --> LISTEN: socket()+listen()
        LISTEN --> SYN_RECV: 收到 SYN
        SYN_RECV --> ESTABLISHED: 收到 ACK
    }
    
    state client_side {
        CLOSED --> SYN_SENT: connect()
        SYN_SENT --> ESTABLISHED: 收到 SYN+ACK
    }
    
    ESTABLISHED --> FIN_WAIT1: close()
    FIN_WAIT1 --> FIN_WAIT2: 收到 ACK
    FIN_WAIT2 --> TIME_WAIT: 收到 FIN
    TIME_WAIT --> CLOSED: 超时
    
    ESTABLISHED --> CLOSE_WAIT: 收到 FIN
    CLOSE_WAIT --> LAST_ACK: close()
    LAST_ACK --> CLOSED: 收到 ACK
    
    note right of ESTABLISHED
        数据传输状态
    end note
```

## 📊 协议栈层次

| 层 | 协议 | 结构 |
|----|------|------|
| 套接字层 | Socket | `struct socket` |
| 传输层 | TCP/UDP | `struct tcphdr` |
| 网络层 | IPv4/IPv6 | `struct iphdr` |
| 链路层 | Ethernet | `struct ethhdr` |

## 🔧 网络调试

```bash
# 查看连接
ss -tuln
netstat -an

# 抓包
tcpdump -i eth0 port 80

# 路由查看
ip route show

# 统计信息
cat /proc/net/snmp
```

## 📝 学习笔记

### SKB 操作

```c
// 分配
struct sk_buff *alloc_skb(unsigned int size, gfp_t priority);

// 释放
void kfree_skb(struct sk_buff *skb);

// 数据操作
skb_put()  // 添加数据到尾部
skb_push() // 添加数据到头部
skb_pull() // 从头部移除数据
```

### Netfilter 钩子

```c
NF_INET_PRE_ROUTING   // 路由前
NF_INET_LOCAL_IN      // 本地输入
NF_INET_FORWARD       // 转发
NF_INET_LOCAL_OUT     // 本地输出
NF_INET_POST_ROUTING  // 路由后
```

### 性能优化

1. **NAPI** - 减少中断开销
2. **零拷贝** - sendfile/splice
3. **RSS** - 多队列网卡
4. **XDP** - 快速数据包处理
