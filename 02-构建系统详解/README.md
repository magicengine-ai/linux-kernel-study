# 02-构建系统详解 - 学习资料

## 📊 构建流程图

### 内核编译流程

```mermaid
flowchart TD
    A[make menuconfig] --> B[生成.config]
    B --> C[make -j nproc]
    C --> D[编译各子系统]
    
    subgraph 编译过程
        D --> D1[编译 arch/]
        D --> D2[编译 kernel/]
        D --> D3[编译 drivers/]
        D --> D4[编译 fs/]
        D --> D5[编译 net/]
    end
    
    D1 --> E[链接 vmlinux]
    D2 --> E
    D3 --> E
    D4 --> E
    D5 --> E
    
    E --> F[生成 System.map]
    F --> G[压缩内核 bzImage]
    G --> H[make modules_install]
    H --> I[make install]
    
    style A fill:#e1f5ff
    style I fill:#e8f5e9
```

### Kbuild 系统架构

```mermaid
graph LR
    A[顶层 Makefile] --> B[Kbuild 系统]
    B --> C[arch/Makefile]
    B --> D[drivers/Makefile]
    B --> E[fs/Makefile]
    B --> F[net/Makefile]
    
    C --> G[对象文件]
    D --> G
    E --> G
    F --> G
    
    G --> H[built-in.a]
    H --> I[vmlinux]
    
    style A fill:#ffebd9
    style I fill:#c8e6c9
```

### 配置系统

```mermaid
sequenceDiagram
    participant User as 用户
    participant Menu as menuconfig
    participant Kconfig as Kconfig 文件
    participant Config as .config
    
    User->>Menu: 选择配置项
    Menu->>Kconfig: 读取配置定义
    Kconfig-->>Menu: 返回配置选项
    Menu->>Config: 写入配置
    Config-->>User: 配置完成
```

## 📁 目录说明

| 文件 | 大小 | 作用 |
|------|------|------|
| `Makefile` | 74KB | 主构建脚本 |
| `Kbuild` | 2.8KB | 构建规则 |
| `Kconfig` | 582B | 配置选项 |

## 🔧 常用命令

```bash
# 配置
make menuconfig      # 图形配置
make defconfig       # 默认配置

# 编译
make -j$(nproc)      # 并行编译
make modules         # 编译模块

# 安装
make modules_install # 安装模块
make install         # 安装内核

# 清理
make clean           # 清理构建
make mrproper        # 彻底清理
```

## 📝 学习笔记

### Kbuild 语法

```makefile
# 内置对象
obj-y += foo.o

# 模块对象
obj-m += bar.o

# 条件编译
obj-$(CONFIG_EXT4_FS) += ext4/

# 复合对象
obj-y += net/ drivers/
```

### 优化技巧

1. 使用 `ccache` 加速编译
2. 并行编译 `-j$(nproc)`
3. 增量编译只编译修改部分
4. 使用 `cconfig` 管理配置
