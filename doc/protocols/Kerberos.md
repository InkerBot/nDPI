# Kerberos 协议文档

## 协议概述

- **协议全称**: Kerberos Network Authentication Protocol (Kerberos 网络认证协议)
- **协议 ID**: NDPI_PROTOCOL_KERBEROS (111)
- **协议类别**: 认证和安全协议
- **传输层**: TCP/UDP (端口 88)

## 简介

Kerberos 是一种网络认证协议，使用密钥加密技术为客户端/服务器应用程序提供强认证。它是 MIT 开发的，广泛应用于企业环境，特别是 Windows Active Directory 域认证。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "Kerberos",
    "kerberos": {
      "hostname": "workstation01",
      "domain": "EXAMPLE.COM",
      "username": "jsmith"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| hostname | string | 客户端主机名 | "workstation01" |
| domain | string | Kerberos 域（realm） | "EXAMPLE.COM" |
| username | string | 用户主体名称 | "jsmith" |

### Kerberos 术语

| 术语 | 说明 |
|------|------|
| Realm | Kerberos 域，通常为大写的域名 |
| Principal | 主体，Kerberos 中的身份标识 |
| KDC | Key Distribution Center，密钥分发中心 |
| TGT | Ticket Granting Ticket，票据授予票据 |
| Service Ticket | 服务票据，用于访问特定服务 |

## 实际示例

### 示例 1: 用户认证请求

```json
{
  "ndpi": {
    "protocol": "Kerberos",
    "kerberos": {
      "hostname": "WIN10-PC",
      "domain": "CORP.EXAMPLE.COM",
      "username": "john.doe"
    }
  }
}
```

### 示例 2: 服务票据请求

```json
{
  "ndpi": {
    "protocol": "Kerberos",
    "kerberos": {
      "hostname": "fileserver01",
      "domain": "INTERNAL.COMPANY.NET",
      "username": "admin"
    }
  }
}
```

### 示例 3: 跨域认证

```json
{
  "ndpi": {
    "protocol": "Kerberos",
    "kerberos": {
      "hostname": "laptop-user",
      "domain": "PROD.ENTERPRISE.ORG",
      "username": "svc_account"
    }
  }
}
```

### 示例 4: 最小信息

```json
{
  "ndpi": {
    "protocol": "Kerberos",
    "kerberos": {
      "domain": "EXAMPLE.LOCAL"
    }
  }
}
```

## 注意事项

### 1. Kerberos 域名（Realm）

- Realm 通常使用大写字母
- 通常与 DNS 域名对应（但大写）
- 例如：DNS 域 `example.com` 对应 Kerberos realm `EXAMPLE.COM`

### 2. 主体名称格式

Kerberos 主体名称格式：
- 用户主体：`username@REALM`
- 服务主体：`service/hostname@REALM`
- 示例：
  - `jsmith@EXAMPLE.COM`
  - `HTTP/www.example.com@EXAMPLE.COM`
  - `cifs/fileserver.example.com@EXAMPLE.COM`

### 3. 字段可用性

- 并非所有 Kerberos 消息都包含所有字段
- `username` 在某些服务票据请求中可能不存在
- `hostname` 可能从客户端信息中提取

### 4. Kerberos 消息类型

Kerberos 协议包含多种消息类型：
- AS-REQ/AS-REP - 认证服务请求/响应
- TGS-REQ/TGS-REP - 票据授予服务请求/响应
- AP-REQ/AP-REP - 应用请求/响应
- KRB-ERROR - 错误消息

### 5. 端口使用

- TCP/UDP 端口 88 - Kerberos 认证
- TCP/UDP 端口 464 - Kerberos 密码修改
- TCP/UDP 端口 749 - Kerberos 管理

### 6. 与 Active Directory 的关系

在 Windows 环境中：
- Active Directory 使用 Kerberos 作为默认认证协议
- 域控制器充当 KDC 角色
- Windows 客户端自动使用 Kerberos 认证

### 7. 安全性

- Kerberos 使用对称密钥加密
- 票据有时间限制（通常 10 小时）
- 支持单点登录（SSO）
- 防止重放攻击（使用时间戳）

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_KERBEROS, 行 1645-1651)
- **协议检测**: `src/lib/protocols/kerberos.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_KERBEROS:
  ndpi_serialize_start_of_block(serializer, "kerberos");
  ndpi_serialize_string_string(serializer, "hostname", flow->protos.kerberos.hostname);
  ndpi_serialize_string_string(serializer, "domain", flow->protos.kerberos.domain);
  ndpi_serialize_string_string(serializer, "username", flow->protos.kerberos.username);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_kerberos()` - Kerberos 协议检测器

## 参考资料

- [RFC 4120 - The Kerberos Network Authentication Service (V5)](https://tools.ietf.org/html/rfc4120)
- [RFC 6649 - Deprecate DES, RC4-HMAC-EXP, and Other Weak Cryptographic Algorithms in Kerberos](https://tools.ietf.org/html/rfc6649)
- [Microsoft Kerberos Documentation](https://docs.microsoft.com/en-us/windows-server/security/kerberos/kerberos-authentication-overview)
- [MIT Kerberos](https://web.mit.edu/kerberos/)
- [nDPI Kerberos 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/kerberos.c)

## 常见用例

### 1. Active Directory 域认证

Windows 域环境中的用户登录和资源访问认证。

### 2. 单点登录（SSO）

用户一次认证后访问多个服务，无需重复输入密码。

### 3. 服务到服务认证

应用服务间的相互认证和授权。

### 4. Linux/Unix 集成

Unix/Linux 系统加入 Windows 域或使用 MIT Kerberos。

### 5. Web 应用认证

Web 应用使用 SPNEGO/Kerberos 实现 SSO。

### 6. 安全审计

监控 Kerberos 认证活动，检测异常登录行为。

### 7. 凭据盗窃检测

检测 Pass-the-Ticket、Golden Ticket 等攻击。

### 8. 网络故障排查

诊断域认证问题和 KDC 连接故障。

## Kerberos 攻击检测

常见的 Kerberos 攻击模式：
- **Pass-the-Ticket**: 窃取并重用 Kerberos 票据
- **Golden Ticket**: 伪造 TGT 获取任意权限
- **Silver Ticket**: 伪造服务票据
- **Kerberoasting**: 请求服务票据并离线破解
- **AS-REP Roasting**: 针对禁用预认证的账户

监控以下异常行为：
- 大量票据请求
- 异常服务主体访问
- 加密类型降级
- 跨域异常认证
