# SMTP 协议文档

## 协议概述

- **协议全称**: Simple Mail Transfer Protocol (简单邮件传输协议)
- **协议 ID**: NDPI_PROTOCOL_MAIL_SMTP (3)
- **协议类别**: 邮件协议
- **传输层**: TCP (端口 25, 587, 465)

## 简介

SMTP 是用于发送电子邮件的互联网标准协议。它定义了邮件客户端如何向邮件服务器提交邮件，以及邮件服务器之间如何转发邮件。nDPI 可以提取 SMTP 认证信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "SMTP",
    "smtp": {
      "user": "john.doe@example.com",
      "password": "********",
      "auth_failed": 0
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| user | string | SMTP 认证用户名 | "john.doe@example.com" |
| password | string | SMTP 认证密码 | "mypassword" |
| auth_failed | uint32 | 认证是否失败 (0=成功, 1=失败) | 0 |

### SMTP 端口

| 端口 | 用途 | 加密 |
|------|------|------|
| 25 | SMTP (服务器间传输) | 明文或 STARTTLS |
| 587 | SMTP Submission (客户端提交) | STARTTLS |
| 465 | SMTPS (废弃，但仍在使用) | SSL/TLS 包装 |

## 实际示例

### 示例 1: 成功认证

```json
{
  "ndpi": {
    "protocol": "SMTP",
    "smtp": {
      "user": "alice@company.com",
      "password": "SecurePass123",
      "auth_failed": 0
    }
  }
}
```

### 示例 2: 认证失败

```json
{
  "ndpi": {
    "protocol": "SMTP",
    "smtp": {
      "user": "bob@example.org",
      "password": "wrongpass",
      "auth_failed": 1
    }
  }
}
```

### 示例 3: 仅用户名

```json
{
  "ndpi": {
    "protocol": "SMTP",
    "smtp": {
      "user": "user@domain.com",
      "auth_failed": 0
    }
  }
}
```

## 注意事项

### 1. 明文密码风险

- SMTP AUTH 在没有 TLS 时以 BASE64 编码传输
- BASE64 不是加密，可以轻易解码
- **强烈建议使用 STARTTLS 或 SMTPS**

### 2. 认证机制

常见的 SMTP 认证方法：
- **PLAIN** - 明文认证（BASE64 编码）
- **LOGIN** - 与 PLAIN 类似
- **CRAM-MD5** - 质询-响应认证
- **DIGEST-MD5** - 更安全的质询-响应
- **XOAUTH2** - OAuth 2.0 认证

### 3. STARTTLS

- 通过 `STARTTLS` 命令升级到 TLS
- 在端口 25 或 587 上先建立明文连接
- 然后协商升级为加密连接

### 4. 字段可用性

- `user` 和 `password` 仅在使用认证时存在
- 服务器间的邮件转发通常不需要认证
- `auth_failed` 指示认证结果

### 5. SMTP 命令

常见 SMTP 命令：
- **EHLO/HELO** - 打招呼
- **AUTH** - 认证
- **MAIL FROM** - 发件人地址
- **RCPT TO** - 收件人地址
- **DATA** - 邮件内容
- **QUIT** - 结束会话

### 6. 安全注意事项

- 监控 SMTP 认证失败可检测暴力破解
- 检测明文 SMTP 连接以确保安全策略
- 异常的 SMTP 活动可能表示垃圾邮件或恶意软件

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_MAIL_SMTP, 行 1767-1774)
- **协议检测**: `src/lib/protocols/mail_smtp.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_MAIL_SMTP:
  ndpi_serialize_start_of_block(serializer, "smtp");
  ndpi_serialize_string_string(serializer, "user", flow->l4.tcp.ftp_imap_pop_smtp.username);
  ndpi_serialize_string_string(serializer, "password", flow->l4.tcp.ftp_imap_pop_smtp.password);
  ndpi_serialize_string_uint32(serializer, "auth_failed",
                               flow->l4.tcp.ftp_imap_pop_smtp.auth_failed);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_mail_smtp()` - SMTP 协议检测器

## 参考资料

- [RFC 5321 - Simple Mail Transfer Protocol](https://tools.ietf.org/html/rfc5321)
- [RFC 4954 - SMTP Service Extension for Authentication](https://tools.ietf.org/html/rfc4954)
- [RFC 3207 - SMTP Service Extension for Secure SMTP over TLS](https://tools.ietf.org/html/rfc3207)
- [RFC 6409 - Message Submission for Mail](https://tools.ietf.org/html/rfc6409)
- [nDPI SMTP 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/mail_smtp.c)

## 常见用例

### 1. 邮件发送

邮件客户端（Outlook、Thunderbird）通过 SMTP 发送邮件。

### 2. 服务器间邮件转发

邮件服务器之间使用 SMTP 转发邮件。

### 3. 应用程序邮件

Web 应用、监控系统等通过 SMTP 发送通知邮件。

### 4. 安全审计

监控 SMTP 认证失败，检测账户暴力破解。

### 5. 垃圾邮件检测

分析 SMTP 流量模式识别垃圾邮件发送者。

### 6. 合规性检查

确保所有 SMTP 连接使用 TLS 加密。

### 7. 性能监控

监控 SMTP 服务器响应时间和吞吐量。

## SMTP 状态码

常见 SMTP 响应码：

| 代码 | 含义 |
|------|------|
| 220 | 服务准备就绪 |
| 221 | 服务关闭连接 |
| 235 | 认证成功 |
| 250 | 请求的邮件操作完成 |
| 334 | 等待认证信息 |
| 354 | 开始邮件输入 |
| 421 | 服务不可用 |
| 450 | 邮箱不可用 |
| 500 | 语法错误 |
| 535 | 认证失败 |
| 550 | 邮箱不存在 |

## 相关协议

- [IMAP.md](IMAP.md) - 接收邮件协议
- [POP3.md](POP3.md) - 接收邮件协议
- [TLS.md](TLS.md) - SMTPS 加密
