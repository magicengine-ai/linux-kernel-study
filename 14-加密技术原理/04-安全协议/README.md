# 安全协议详解

> TLS/IPSec/SSH 协议原理

---

## 📋 TLS 协议

### TLS 握手流程

```mermaid
sequenceDiagram
    participant Client
    participant Server
    
    Client->>Server: ClientHello
    Server->>Client: ServerHello
    Server->>Client: Certificate
    Server->>Client: ServerKeyExchange
    Server->>Client: ServerHelloDone
    Client->>Server: ClientKeyExchange
    Client->>Server: ChangeCipherSpec
    Client->>Server: Finished
    Server->>Client: ChangeCipherSpec
    Server->>Client: Finished
    
    Note over Client,Server: 加密通信开始
```

---

## 🔧 IPSec 协议

### IPSec 架构

```mermaid
graph TB
    subgraph IPSec
        A[IKE 密钥交换] --> B[ESP 封装]
        A --> C[AH 认证]
    end
    
    B --> D[隧道模式]
    B --> E[传输模式]
    
    style A fill:#e3f2fd
    style D fill:#c8e6c9
```

---

## ✅ 总结

安全协议核心：

1. **TLS** - Web 安全通信
2. **IPSec** - VPN 隧道
3. **SSH** - 安全远程登录
4. **IKE** - 密钥交换

---

*学习笔记由 全栈工程师 维护*
