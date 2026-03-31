# FTP 协议文档

## 协议概述

- **协议全称**: File Transfer Protocol (文件传输协议)
- **协议 ID**: NDPI_PROTOCOL_FTP_CONTROL (1)
- **协议类别**: 文件传输协议
- **传输层**: TCP (端口 21 控制，20 数据)

## 简介

FTP 是用于在网络上传输文件的标准协议。它使用两个独立的连接：控制连接（用于命令和响应）和数据连接（用于实际文件传输）。nDPI 可以提取 FTP 认证信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "FTP_CONTROL",
    "ftp": {
      "user": "ftpuser",
      "password": "ftppass123",
      "auth_failed": 0
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| user | string | FTP 用户名 | "ftpuser" |
| password | string | FTP 密码 | "ftppass123" |
| auth_failed | uint32 | 认证是否失败 (0=成功, 1=失败) | 0 |

### FTP 端口

| 端口 | 用途 | 模式 |
|------|------|------|
| 21 | FTP 控制连接 | 命令和响应 |
| 20 | FTP 数据连接 | 主动模式数据传输 |
| 动态端口 | FTP 数据连接 | 被动模式数据传输 |

## 实际示例

### 示例 1: 成功认证

```json
{
  "ndpi": {
    "protocol": "FTP_CONTROL",
    "ftp": {
      "user": "admin",
      "password": "admin123",
      "auth_failed": 0
    }
  }
}
```

### 示例 2: 认证失败

```json
{
  "ndpi": {
    "protocol": "FTP_CONTROL",
    "ftp": {
      "user": "anonymous",
      "password": "guest@example.com",
      "auth_failed": 1
    }
  }
}
```

### 示例 3: 匿名登录

```json
{
  "ndpi": {
    "protocol": "FTP_CONTROL",
    "ftp": {
      "user": "anonymous",
      "password": "mozilla@example.com",
      "auth_failed": 0
    }
  }
}
```

### 示例 4: 企业FTP服务器

```json
{
  "ndpi": {
    "protocol": "FTP_CONTROL",
    "ftp": {
      "user": "backup_user",
      "password": "SecureBackup2024",
      "auth_failed": 0
    }
  }
}
```

## 注意事项

### 1. 明文传输风险

- **FTP 以明文传输所有数据**，包括用户名、密码和文件内容
- 任何网络监听者都可以看到凭据和数据
- **强烈建议使用 FTPS 或 SFTP**

### 2. FTP 模式

**主动模式（PORT）**：
- 客户端打开随机端口接收数据
- 服务器从端口 20 连接客户端
- 防火墙可能阻止连接

**被动模式（PASV）**：
- 服务器打开随机端口
- 客户端连接服务器端口
- 更易穿越防火墙

### 3. FTP 命令

常见 FTP 命令：
- **USER** - 指定用户名
- **PASS** - 指定密码
- **PWD** - 显示当前目录
- **CWD** - 改变工作目录
- **LIST** - 列出文件
- **RETR** - 下载文件
- **STOR** - 上传文件
- **DELE** - 删除文件
- **MKD** - 创建目录
- **RMD** - 删除目录
- **QUIT** - 退出

### 4. 匿名 FTP

- 用户名：`anonymous` 或 `ftp`
- 密码：通常是电子邮件地址
- 用于公共文件分发

### 5. FTP vs SFTP vs FTPS

**FTP**：
- 明文传输，不安全
- 使用 TCP 端口 21

**FTPS (FTP over SSL/TLS)**：
- FTP 加密版本
- 端口 21 (显式 TLS) 或 990 (隐式 TLS)
- 使用 SSL/TLS 加密

**SFTP (SSH File Transfer Protocol)**：
- 基于 SSH 的文件传输
- 端口 22
- 完全不同于 FTP，是 SSH 的子系统

### 6. 双连接架构

FTP 使用两个连接：
- **控制连接**：持续存在，传输命令
- **数据连接**：按需建立，传输文件和列表

### 7. ASCII vs Binary 模式

- **ASCII 模式**：文本文件传输，处理换行符转换
- **Binary 模式**：二进制文件传输，不做任何转换

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_FTP_CONTROL, 行 1776-1782)
- **协议检测**: `src/lib/protocols/ftp_control.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_FTP_CONTROL:
  ndpi_serialize_start_of_block(serializer, "ftp");
  ndpi_serialize_string_string(serializer, "user", flow->l4.tcp.ftp_imap_pop_smtp.username);
  ndpi_serialize_string_string(serializer, "password", flow->l4.tcp.ftp_imap_pop_smtp.password);
  ndpi_serialize_string_uint32(serializer, "auth_failed", flow->l4.tcp.ftp_imap_pop_smtp.auth_failed);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_ftp_control()` - FTP 控制连接检测器

## 参考资料

- [RFC 959 - File Transfer Protocol](https://tools.ietf.org/html/rfc959)
- [RFC 2228 - FTP Security Extensions](https://tools.ietf.org/html/rfc2228)
- [RFC 2428 - FTP Extensions for IPv6 and NATs](https://tools.ietf.org/html/rfc2428)
- [RFC 4217 - Securing FTP with TLS](https://tools.ietf.org/html/rfc4217)
- [nDPI FTP 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/ftp_control.c)

## 常见用例

### 1. 文件分发

公共 FTP 服务器分发软件、文档等。

### 2. 网站维护

Web 开发者使用 FTP 上传网站文件。

### 3. 备份

系统备份到远程 FTP 服务器。

### 4. 数据交换

企业间交换文件和数据。

### 5. 安全审计

监控 FTP 登录尝试，检测未授权访问。

### 6. 合规性检查

检测使用不安全的 FTP 协议，要求迁移到 FTPS/SFTP。

### 7. 性能监控

监控 FTP 服务器性能和传输速度。

## FTP 响应码

常见 FTP 响应码：

| 代码 | 类别 | 含义 |
|------|------|------|
| 1xx | 正面初步答复 | 操作已开始 |
| 2xx | 正面完成答复 | 操作成功完成 |
| 3xx | 正面中间答复 | 需要更多信息 |
| 4xx | 暂时否定答复 | 临时错误 |
| 5xx | 永久否定答复 | 永久错误 |

**具体响应码**：
- 220 - 服务就绪
- 230 - 用户登录成功
- 331 - 用户名正确，需要密码
- 425 - 无法打开数据连接
- 530 - 未登录
- 550 - 文件不可用

## 安全最佳实践

1. **迁移到安全协议**
   - 使用 FTPS 或 SFTP 替代 FTP
   - 禁用明文 FTP

2. **访问控制**
   - 限制匿名访问
   - 使用强密码
   - 实施 IP 白名单

3. **监控和审计**
   - 记录所有 FTP 活动
   - 监控认证失败
   - 检测异常传输模式

4. **防火墙配置**
   - 配置 FTP 协议感知防火墙
   - 限制被动模式端口范围

5. **定期更新**
   - 保持 FTP 服务器软件更新
   - 修补已知安全漏洞

## FTP 替代方案

现代文件传输建议：

| 协议 | 安全性 | 性能 | 使用场景 |
|------|--------|------|----------|
| SFTP | 高 | 好 | 推荐用于安全文件传输 |
| FTPS | 高 | 好 | FTP 加密版本 |
| SCP | 高 | 好 | 简单文件复制 |
| HTTP/HTTPS | 中-高 | 优秀 | Web 文件传输 |
| rsync | 中 | 优秀 | 增量同步 |

## 相关协议

- [SSH.md](SSH.md) - SFTP 基于 SSH
- [TLS.md](TLS.md) - FTPS 使用 TLS 加密
- [HTTP.md](HTTP.md) - Web 文件传输
