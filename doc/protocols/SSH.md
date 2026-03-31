# SSH 协议文档

## 协议概述

- **协议全称**: Secure Shell (安全外壳协议)
- **协议 ID**: NDPI_PROTOCOL_SSH (92)
- **协议类别**: 远程访问和安全协议
- **传输层**: TCP (默认端口 22)

## 简介

SSH 是一种加密网络协议，用于在不安全的网络上安全地运行网络服务。最常见的用途是远程登录和命令行执行。nDPI 可以提取 SSH 握手中的客户端和服务器签名，以及 HASSH 指纹信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "SSH",
    "ssh": {
      "client_signature": "SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.5",
      "server_signature": "SSH-2.0-OpenSSH_7.4",
      "hassh_client": "ec7378c1a92f5a8dde7e8b7a1ddf33bc",
      "hassh_server": "b12d2871a1189eff20364cf5333619ee",
      "key_exchange_algorithms": {
        "client": [
          "curve25519-sha256",
          "ecdh-sha2-nistp256",
          "diffie-hellman-group14-sha256"
        ],
        "server": [
          "curve25519-sha256@libssh.org",
          "ecdh-sha2-nistp256"
        ],
        "key_exchange_method": "curve25519-sha256"
      }
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| client_signature | string | SSH 客户端版本字符串 | "SSH-2.0-OpenSSH_8.2p1" |
| server_signature | string | SSH 服务器版本字符串 | "SSH-2.0-OpenSSH_7.4" |
| hassh_client | string | HASSH 客户端指纹 | "ec7378c1a92f5a8dde7e8b7a1ddf33bc" |
| hassh_server | string | HASSH 服务器指纹 | "b12d2871a1189eff20364cf5333619ee" |
| key_exchange_algorithms | object | 密钥交换算法信息（可选） | 见下文 |

### 密钥交换算法字段（可选）

当启用 `ssh_hassh_data_enabled` 配置时，会包含以下字段：

| 字段名 | 类型 | 说明 |
|--------|------|------|
| client | array | 客户端支持的密钥交换算法列表 |
| server | array | 服务器支持的密钥交换算法列表 |
| key_exchange_method | string | 协商使用的密钥交换方法 |

### SSH 版本标识

SSH 版本字符串格式：`SSH-<协议版本>-<软件版本> [注释]`

**协议版本**：
- `SSH-1.x` - SSH 协议版本 1（已弃用，不安全）
- `SSH-2.0` - SSH 协议版本 2（当前标准）

**常见 SSH 实现**：
- OpenSSH - 最流行的开源实现
- PuTTY - Windows SSH 客户端
- Dropbear - 轻量级 SSH 服务器
- libssh - SSH 协议库

## 实际示例

### 示例 1: 标准 SSH 连接（基本信息）

```json
{
  "ndpi": {
    "protocol": "SSH",
    "ssh": {
      "client_signature": "SSH-2.0-OpenSSH_8.9p1 Ubuntu-3ubuntu0.1",
      "server_signature": "SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.5",
      "hassh_client": "92674389fa1e47a27ddd8d9b63ecd42b",
      "hassh_server": "b12d2871a1189eff20364cf5333619ee"
    }
  }
}
```

### 示例 2: SSH 连接（包含密钥交换信息）

```json
{
  "ndpi": {
    "protocol": "SSH",
    "ssh": {
      "client_signature": "SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.5",
      "server_signature": "SSH-2.0-OpenSSH_7.4",
      "hassh_client": "ec7378c1a92f5a8dde7e8b7a1ddf33bc",
      "hassh_server": "b12d2871a1189eff20364cf5333619ee",
      "key_exchange_algorithms": {
        "client": [
          "curve25519-sha256",
          "curve25519-sha256@libssh.org",
          "ecdh-sha2-nistp256",
          "ecdh-sha2-nistp384",
          "diffie-hellman-group-exchange-sha256",
          "diffie-hellman-group14-sha256"
        ],
        "server": [
          "curve25519-sha256@libssh.org",
          "ecdh-sha2-nistp256",
          "ecdh-sha2-nistp384",
          "diffie-hellman-group-exchange-sha256"
        ],
        "key_exchange_method": "curve25519-sha256"
      }
    }
  }
}
```

### 示例 3: PuTTY 客户端

```json
{
  "ndpi": {
    "protocol": "SSH",
    "ssh": {
      "client_signature": "SSH-2.0-PuTTY_Release_0.76",
      "server_signature": "SSH-2.0-OpenSSH_8.2p1",
      "hassh_client": "a59fde5b4cb05d813f5b6b5e1f8ff7f5",
      "hassh_server": "b12d2871a1189eff20364cf5333619ee"
    }
  }
}
```

### 示例 4: Git over SSH

```json
{
  "ndpi": {
    "protocol": "SSH",
    "ssh": {
      "client_signature": "SSH-2.0-OpenSSH_8.2p1",
      "server_signature": "SSH-2.0-babeld-5e8c5c67",
      "hassh_client": "92674389fa1e47a27ddd8d9b63ecd42b",
      "hassh_server": "b03a4c82d403bb3e0553c0c4e4b0e6cb"
    }
  }
}
```

## 注意事项

### 1. HASSH 指纹

HASSH 是 SSH 客户端和服务器的指纹识别方法：
- 基于 SSH 握手中的算法列表
- 用于识别特定的 SSH 客户端/服务器实现
- 可用于威胁检测和应用识别
- 类似于 TLS 的 JA3 指纹

### 2. 配置选项

nDPI 的 SSH 相关配置：
- `ssh_hassh_fingerprint_enabled` - 启用 HASSH 指纹（默认）
- `ssh_hassh_data_enabled` - 启用详细的算法列表

### 3. 版本字符串

版本字符串可能包含：
- SSH 软件名称和版本
- 操作系统信息
- 自定义注释

例如：`SSH-2.0-OpenSSH_8.2p1 Ubuntu-4ubuntu0.5`
- 软件：OpenSSH 8.2p1
- 系统：Ubuntu
- 补丁：ubuntu0.5

### 4. 安全考虑

- SSH-1 协议已弃用，存在严重安全漏洞
- 应确保使用 SSH-2.0
- 旧版本 SSH 软件可能存在已知漏洞

### 5. 密钥交换算法

常见的密钥交换算法（按安全性排序）：
1. `curve25519-sha256` - 推荐，基于椭圆曲线
2. `ecdh-sha2-nistp256` - 基于 NIST P-256 曲线
3. `diffie-hellman-group14-sha256` - DH 组 14
4. `diffie-hellman-group-exchange-sha256` - 动态 DH

### 6. 字段可用性

- `hassh_client` 和 `hassh_server` 需要 `ssh_hassh_fingerprint_enabled` 配置
- `key_exchange_algorithms` 需要 `ssh_hassh_data_enabled` 配置
- 如果配置禁用，这些字段可能不存在

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_SSH, 行 1924-1951)
- **协议检测**: `src/lib/protocols/ssh.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_SSH:
  ndpi_serialize_start_of_block(serializer, "ssh");
  ndpi_serialize_string_string(serializer, "client_signature", flow->protos.ssh.client_signature);
  ndpi_serialize_string_string(serializer, "server_signature", flow->protos.ssh.server_signature);

  if(ndpi_struct->cfg.ssh_hassh_fingerprint_enabled) {
    ndpi_serialize_string_string(serializer, "hassh_client", flow->protos.ssh.hassh_client);
    ndpi_serialize_string_string(serializer, "hassh_server", flow->protos.ssh.hassh_server);
  }

  if(ndpi_struct->cfg.ssh_hassh_data_enabled) {
    // 序列化密钥交换算法
  }

  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_ssh()` - SSH 协议检测器
- `ndpi_ssh_hassh_fingerprint()` - 计算 HASSH 指纹
- `ndpi_ssh_serialize_csv()` - 序列化算法列表

## 参考资料

- [RFC 4251 - The Secure Shell (SSH) Protocol Architecture](https://tools.ietf.org/html/rfc4251)
- [RFC 4252 - SSH Authentication Protocol](https://tools.ietf.org/html/rfc4252)
- [RFC 4253 - SSH Transport Layer Protocol](https://tools.ietf.org/html/rfc4253)
- [HASSH - Profiling SSH Clients and Servers](https://github.com/salesforce/hassh)
- [OpenSSH](https://www.openssh.com/)
- [nDPI SSH 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/ssh.c)

## 常见用例

### 1. 远程服务器管理

系统管理员远程登录服务器执行命令。

### 2. 安全文件传输

使用 SCP 或 SFTP 安全传输文件。

### 3. 端口转发/隧道

创建加密隧道转发其他协议流量。

### 4. Git 版本控制

通过 SSH 访问 Git 仓库。

### 5. 自动化脚本

无密码 SSH（密钥认证）用于自动化任务。

### 6. 威胁检测

使用 HASSH 指纹识别恶意 SSH 客户端或服务器。

### 7. 资产识别

基于 SSH 版本字符串识别服务器和操作系统。

### 8. 合规性检查

确保网络中不使用已弃用的 SSH-1 协议。
