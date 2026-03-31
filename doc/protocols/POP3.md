# POP3 协议文档

## 协议概述

- **协议全称**: Post Office Protocol version 3 (邮局协议版本 3)
- **协议 ID**: NDPI_PROTOCOL_MAIL_POP (2)
- **协议类别**: 邮件协议
- **传输层**: TCP (端口 110, 995)

## 简介

POP3 是用于从邮件服务器检索电子邮件的简单协议。与 IMAP 不同，POP3 通常将邮件下载到本地客户端并从服务器删除，适合单一设备访问邮箱的场景。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "POP",
    "pop": {
      "user": "bob@example.com",
      "password": "mypassword",
      "auth_failed": 0
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| user | string | POP3 认证用户名 | "bob@example.com" |
| password | string | POP3 认证密码 | "mypassword" |
| auth_failed | uint32 | 认证是否失败 (0=成功, 1=失败) | 0 |

### POP3 端口

| 端口 | 用途 | 加密 |
|------|------|------|
| 110 | POP3 | 明文或 STLS |
| 995 | POP3S | SSL/TLS 包装 |

## 实际示例

### 示例 1: 成功认证

```json
{
  "ndpi": {
    "protocol": "POP",
    "pop": {
      "user": "user@mail.com",
      "password": "MySecretPass789",
      "auth_failed": 0
    }
  }
}
```

### 示例 2: 认证失败

```json
{
  "ndpi": {
    "protocol": "POP",
    "pop": {
      "user": "admin@company.net",
      "password": "invalidpass",
      "auth_failed": 1
    }
  }
}
```

### 示例 3: 企业邮箱

```json
{
  "ndpi": {
    "protocol": "POP",
    "pop": {
      "user": "john.doe@enterprise.org",
      "auth_failed": 0
    }
  }
}
```

## 注意事项

### 1. POP3 vs IMAP

**POP3 特点**：
- 邮件下载后通常从服务器删除
- 适合单一设备访问
- 协议简单，资源占用少
- 离线访问邮件

**IMAP 优势**：
- 邮件保留在服务器
- 多设备同步
- 服务器端搜索和管理
- 更灵活的文件夹管理

### 2. POP3 工作模式

- **默认模式**：下载并删除
- **保留副本**：下载但不删除（需客户端配置）
- 一次性操作，不保持会话状态

### 3. 认证方法

POP3 支持的认证：
- **USER/PASS** - 基本用户名密码
- **APOP** - 更安全的认证（使用 MD5）
- **AUTH** - SASL 认证机制
- **CRAM-MD5** - 质询-响应认证

### 4. STLS (STARTTLS)

- 通过 `STLS` 命令升级到 TLS
- 在端口 110 上先建立明文连接
- 然后协商升级为加密连接

### 5. 明文密码风险

- 未加密的 POP3 连接会暴露凭据
- USER/PASS 命令以明文传输
- **强烈建议使用 POP3S 或 STLS**

### 6. POP3 命令

常见 POP3 命令：
- **USER** - 指定用户名
- **PASS** - 指定密码
- **STAT** - 获取邮箱统计信息
- **LIST** - 列出邮件
- **RETR** - 检索邮件
- **DELE** - 标记删除邮件
- **RSET** - 重置会话
- **QUIT** - 退出并提交删除

### 7. POP3 状态

POP3 会话有三个状态：
- **授权** - 连接建立，进行认证
- **事务** - 认证成功，处理邮件
- **更新** - 执行 QUIT 后，提交删除操作

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_MAIL_POP, 行 1758-1765)
- **协议检测**: `src/lib/protocols/mail_pop.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_MAIL_POP:
  ndpi_serialize_start_of_block(serializer, "pop");
  ndpi_serialize_string_string(serializer, "user", flow->l4.tcp.ftp_imap_pop_smtp.username);
  ndpi_serialize_string_string(serializer, "password", flow->l4.tcp.ftp_imap_pop_smtp.password);
  ndpi_serialize_string_uint32(serializer, "auth_failed",
                               flow->l4.tcp.ftp_imap_pop_smtp.auth_failed);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_mail_pop()` - POP3 协议检测器

## 参考资料

- [RFC 1939 - Post Office Protocol - Version 3](https://tools.ietf.org/html/rfc1939)
- [RFC 2595 - Using TLS with IMAP, POP3 and ACAP](https://tools.ietf.org/html/rfc2595)
- [RFC 1734 - POP3 AUTHentication command](https://tools.ietf.org/html/rfc1734)
- [RFC 2449 - POP3 Extension Mechanism](https://tools.ietf.org/html/rfc2449)
- [nDPI POP3 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/mail_pop.c)

## 常见用例

### 1. 邮件客户端

传统邮件客户端（Outlook Express、Thunderbird）下载邮件。

### 2. 单设备邮件访问

个人电脑上离线访问邮件。

### 3. 邮件备份

定期下载邮件进行本地备份。

### 4. 移动设备

某些移动邮件应用使用 POP3 下载邮件以节省服务器空间。

### 5. ISP 邮箱

Internet 服务提供商提供的基础邮箱服务。

### 6. 安全审计

监控 POP3 认证失败，检测暴力破解攻击。

### 7. 合规性检查

确保所有 POP3 连接使用加密传输。

## POP3 响应码

常见 POP3 响应：

| 响应 | 含义 |
|------|------|
| +OK | 命令成功 |
| -ERR | 命令失败 |

示例：
- `+OK POP3 server ready`
- `+OK 5 messages (1024 octets)`
- `-ERR Invalid password`

## POP3 扩展

可选的 POP3 扩展（CAPA 命令）：
- **TOP** - 获取邮件头部和前 N 行
- **UIDL** - 获取邮件的唯一标识符
- **SASL** - SASL 认证支持
- **RESP-CODES** - 扩展响应码
- **LOGIN-DELAY** - 登录延迟
- **PIPELINING** - 命令流水线
- **EXPIRE** - 邮件过期策略
- **IMPLEMENTATION** - 服务器实现信息

## 安全最佳实践

1. **使用 POP3S (端口 995)** 或 STLS
2. **避免使用明文 USER/PASS 认证**
3. **监控认证失败**以检测攻击
4. **限制登录尝试次数**
5. **使用强密码策略**
6. **定期审计 POP3 日志**
7. **考虑迁移到 IMAP**（更现代的选择）

## POP3 的局限性

- 不支持服务器端邮件管理
- 不适合多设备访问
- 不支持文件夹同步
- 搜索功能有限
- 状态信息不保留

## 迁移建议

对于现代邮件系统，建议：
1. **优先使用 IMAP** 而不是 POP3
2. 如必须使用 POP3，启用"保留副本"选项
3. 使用加密连接（POP3S 或 STLS）
4. 考虑使用 OAuth 2.0 认证

## 相关协议

- [SMTP.md](SMTP.md) - 发送邮件协议
- [IMAP.md](IMAP.md) - 更现代的接收邮件协议
- [TLS.md](TLS.md) - POP3S 加密
