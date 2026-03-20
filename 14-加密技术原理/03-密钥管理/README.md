# 密钥管理与安全存储

> 密钥生命周期和安全实践

---

## 📋 密钥管理概述

```mermaid
flowchart LR
    A[密钥生成] --> B[密钥存储]
    B --> C[密钥使用]
    C --> D[密钥轮换]
    D --> E[密钥销毁]
    
    style A fill:#e3f2fd
    style E fill:#ffcdd2
```

---

## 🔧 密钥存储方式

### 内核密钥环

```bash
# 添加密钥到密钥环
keyctl add user mykey "secret_data" @u

# 查看密钥环
keyctl show

# 读取密钥
keyctl print <key_id>

# 删除密钥
keyctl unlink <key_id>
```

### TPM 芯片

```bash
# TPM 工具
tpm2_createprimary -C o -c primary.ctx
tpm2_create -C primary.ctx -u key.pub -r key.priv
tpm2_load -C primary.ctx -u key.pub -r key.priv -c key.ctx

# 使用 TPM 加密
echo "secret" | tpm2_encryptdecrypt -c key.ctx -o encrypted.dat
```

---

## ✅ 总结

密钥管理核心：

1. **密钥环** - 内核密钥存储
2. **TPM** - 硬件安全模块
3. **密钥轮换** - 定期更新
4. **安全销毁** - 彻底清除

---

*学习笔记由 全栈工程师 维护*
