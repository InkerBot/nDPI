# IMAP 协议文档

## 协议概述

- **协议全称**: Internet Message Access Protocol (互联网邮件访问协议)
- **协议 ID**: NDPI_PROTOCOL_MAIL_IMAP (4)
- **协议类别**: 邮件协议
- **传输层**: TCP (端口 143, 993)

## 简介

IMAP 是用于从邮件服务器检索电子邮件的互联网标准协议。与 POP3 不同，IMAP 允许邮件保留在服务器上，支持多个客户端访问同一邮箱，并提供邮件文件夹管理功能。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "IMAP",
    "imap": {
      "user": "alice@example.com",
      "password": "mypassword",
      "auth_failed": 0
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| user | string | IMAP 认证用户名 | "alice@example.com" |
| password | string | IMAP 认证密码 | "mypassword" |
| auth_failed | uint32 | 认证是否失败 (0=成功, 1=失败) | 0 |

### IMAP 端口

| 端口 | 用途 | 加密 |
|------|------|------|
| 143 | IMAP | 明文或 STARTTLS |
| 993 | IMAPS | SSL/TLS 包装 |

## 实际示例

### 示例 1: 成功认证

```json
{
  "ndpi": {
    "protocol": "IMAP",
    "imap": {
      "user": "john.smith@company.org",
      "password": "SecurePass456",
      "auth_failed": 0
    }
  }
}
```

### 示例 2: 认证失败

```json
{
  "ndpi": {
    "protocol": "IMAP",
    "imap": {
      "user": "test@example.com",
      "password": "wrongpassword",
      "auth_failed": 1
    }
  }
}
```

### 示例 3: 企业邮箱

```json
{
  "ndpi": {
    "protocol": "IMAP",
    "imap": {
      "user": "employee@enterprise.com",
      "auth_failed": 0
    }
  }
}
```

## 注意事项

### 1. IMAP vs POP3

**IMAP 优势**：
- 邮件保留在服务器上
- 支持多客户端同步
- 支持服务器端邮件搜索
- 支持文件夹管理
- 支持部分邮件下载

**POP3 特点**：
- 邮件下载到本地后通常删除
- 适合单一客户端访问
- 更简单，占用资源更少

### 2. 认证机制

IMAP 支持多种认证方法：
- **PLAIN** - 明文认证（BASE64 编码）
- **LOGIN** - 传统登录方式
- **CRAM-MD5** - 质询-响应认证
- **DIGEST-MD5** - 更安全的认证
- **GSSAPI** - Kerberos 认证
- **XOAUTH2** - OAuth 2.0 认证（Gmail 等）

### 3. STARTTLS

- 通过 `STARTTLS` 命令升级连接
- 在端口 143 上先建立明文连接
- 然后协商升级为 TLS 加密

### 4. 明文密码风险

- 未加密的 IMAP 连接会暴露凭据
- BASE64 编码不是加密
- **强烈建议使用 IMAPS 或 STARTTLS**

### 5. IMAP 命令

常见 IMAP 命令：
- **LOGIN** - 登录认证
- **AUTHENTICATE** - 使用 SASL 认证
- **SELECT** - 选择邮箱
- **EXAMINE** - 以只读模式打开邮箱
- **FETCH** - 获取邮件
- **SEARCH** - 搜索邮件
- **STORE** - 修改邮件标志
- **EXPUNGE** - 永久删除标记的邮件
- **LOGOUT** - 退出

### 6. IMAP 状态

IMAP 会话有多种状态：
- **未认证** - 连接建立，未登录
- **已认证** - 登录成功，未选择邮箱
- **已选择** - 选择了邮箱，可进行操作
- **注销** - 会话结束

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_MAIL_IMAP, 行 1749-1756)
- **协议检测**: `src/lib/protocols/mail_imap.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_MAIL_IMAP:
  ndpi_serialize_start_of_block(serializer, "imap");
  ndpi_serialize_string_string(serializer, "user", flow->l4.tcp.ftp_imap_pop_smtp.username);
  ndpi_serialize_string_string(serializer, "password", flow->l4.tcp.ftp_imap_pop_smtp.password);
  ndpi_serialize_string_uint32(serializer, "auth_failed",
                               flow->l4.tcp.ftp_imap_pop_smtp.auth_failed);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_mail_imap()` - IMAP 协议检测器

## 参考资料

- [RFC 3501 - Internet Message Access Protocol - Version 4rev1](https://tools.ietf.org/html/rfc3501)
- [RFC 2177 - IMAP4 IDLE command](https://tools.ietf.org/html/rfc2177)
- [RFC 4551 - IMAP Extension for Conditional STORE](https://tools.ietf.org/html/rfc4551)
- [RFC 6855 - IMAP Support for UTF-8](https://tools.ietf.org/html/rfc6855)
- [nDPI IMAP 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/mail_imap.c)

## 常见用例

### 1. 邮件客户端

桌面和移动邮件应用（Outlook、Thunderbird、iOS Mail）。

### 2. Webmail 界面

Gmail、Outlook.com 等 Web 邮件界面后端使用 IMAP。

### 3. 邮件同步

多设备之间同步邮件状态（已读、标记等）。

### 4. 邮件备份

企业邮件备份系统通过 IMAP 访问邮箱。

### 5. 邮件归档

合规性要求的邮件归档系统。

### 6. 安全审计

监控 IMAP 认证失败，检测暴力破解攻击。

### 7. 垃圾邮件过滤

服务器端垃圾邮件过滤器通过 IMAP 访问邮箱。

## IMAP 响应码

常见 IMAP 响应：

| 响应 | 含义 |
|------|------|
| OK | 命令成功完成 |
| NO | 命令失败 |
| BAD | 命令语法错误 |
| PREAUTH | 预认证连接 |
| BYE | 服务器关闭连接 |

## IMAP 扩展

现代 IMAP 服务器支持的扩展：
- **IDLE** - 实时推送新邮件
- **UIDPLUS** - 增强的 UID 操作
- **CONDSTORE** - 条件存储
- **QRESYNC** - 快速重新同步
- **COMPRESS** - 压缩传输
- **METADATA** - 服务器元数据
- **SPECIAL-USE** - 特殊用途邮箱

## 安全最佳实践

1. **使用 IMAPS (端口 993)** 或 STARTTLS
2. **启用强认证机制**（如 OAuth 2.0）
3. **监控认证失败**以检测攻击
4. **限制登录尝试次数**
5. **使用防火墙规则**限制 IMAP 访问
6. **定期审计 IMAP 日志**

## 相关协议

- [SMTP.md](SMTP.md) - 发送邮件协议
- [POP3.md](POP3.md) - 另一种接收邮件协议
- [TLS.md](TLS.md) - IMAPS 加密
