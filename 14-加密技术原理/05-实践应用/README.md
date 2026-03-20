# 加密实践应用

> dm-crypt/加密文件系统/SSL

---

## 📋 磁盘加密

### dm-crypt/LUKS

```bash
# 创建加密卷
cryptsetup luksFormat /dev/sda1

# 打开加密卷
cryptsetup open /dev/sda1 encrypted_volume

# 创建文件系统
mkfs.ext4 /dev/mapper/encrypted_volume

# 挂载
mount /dev/mapper/encrypted_volume /mnt/secure

# 关闭
umount /mnt/secure
cryptsetup close encrypted_volume
```

---

## 🔧 加密文件系统

### eCryptfs

```bash
# 挂载 eCryptfs
mount -t ecryptfs /home/user/encrypted /home/user/plain

# 配置选项
- Cipher: aes
- Key bytes: 16
- Enable plaintext passthrough: n
- Enable filename encryption: y
```

---

## 🔧 SSL/TLS 实践

### OpenSSL 命令

```bash
# 生成私钥
openssl genrsa -out private.key 2048

# 生成证书签名请求
openssl req -new -key private.key -out request.csr

# 生成自签名证书
openssl x509 -req -days 365 -in request.csr \
    -signkey private.key -out certificate.crt

# 验证证书
openssl verify certificate.crt
```

---

## ✅ 总结

加密实践核心：

1. **dm-crypt** - 全盘加密
2. **eCryptfs** - 文件级加密
3. **OpenSSL** - 证书管理
4. **最佳实践** - 安全配置

---

*学习笔记由 全栈工程师 维护*
