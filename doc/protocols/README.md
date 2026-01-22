# nDPI 协议文档索引

本目录包含 nDPI 库支持的主要网络协议的详细 JSON 输出文档。每个协议文档描述了 `ndpi_dpi2json` 函数对该协议的序列化输出格式。

## 使用指南

请先阅读 [ndpi_dpi2json 函数使用指南](../ndpi_dpi2json_guide.md) 了解基本用法。

## 协议分类

### 核心网络协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| DNS | NDPI_PROTOCOL_DNS (5) | 53 | [DNS.md](DNS.md) |
| HTTP | NDPI_PROTOCOL_HTTP (7) | 80, 8080 | [HTTP.md](HTTP.md) |
| DHCP | NDPI_PROTOCOL_DHCP (18) | 67, 68 | [DHCP.md](DHCP.md) |
| NTP | NDPI_PROTOCOL_NTP (9) | 123 | [NTP.md](NTP.md) |
| MDNS | NDPI_PROTOCOL_MDNS (8) | 5353 | [MDNS.md](MDNS.md) |
| SNMP | NDPI_PROTOCOL_SNMP (14) | 161, 162 | [SNMP.md](SNMP.md) |

### 安全协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| TLS | NDPI_PROTOCOL_TLS (91) | 443 | [TLS.md](TLS.md) |
| DTLS | NDPI_PROTOCOL_DTLS (30) | 多种 | [DTLS.md](DTLS.md) |
| SSH | NDPI_PROTOCOL_SSH (92) | 22 | [SSH.md](SSH.md) |
| Kerberos | NDPI_PROTOCOL_KERBEROS (111) | 88 | [Kerberos.md](Kerberos.md) |

### 传输协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| QUIC | NDPI_PROTOCOL_QUIC (188) | 443 | [QUIC.md](QUIC.md) |

### 邮件协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| SMTP | NDPI_PROTOCOL_MAIL_SMTP (3) | 25, 587 | [SMTP.md](SMTP.md) |
| IMAP | NDPI_PROTOCOL_MAIL_IMAP (4) | 143, 993 | [IMAP.md](IMAP.md) |
| POP3 | NDPI_PROTOCOL_MAIL_POP (2) | 110, 995 | [POP3.md](POP3.md) |

### 文件传输协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| FTP | NDPI_PROTOCOL_FTP_CONTROL (1) | 21 | [FTP.md](FTP.md) |
| BitTorrent | NDPI_PROTOCOL_BITTORRENT (37) | 多种 | [BitTorrent.md](BitTorrent.md) |

### VoIP 协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| SIP | NDPI_PROTOCOL_SIP (100) | 5060, 5061 | [SIP.md](SIP.md) |
| STUN | NDPI_PROTOCOL_STUN (78) | 3478 | [STUN.md](STUN.md) |

### 数据库协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| MongoDB | NDPI_PROTOCOL_MONGODB (60) | 27017 | [MongoDB.md](MongoDB.md) |

### 远程访问协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| Telnet | NDPI_PROTOCOL_TELNET (77) | 23 | [Telnet.md](Telnet.md) |

### 网络地址转换协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| NAT-PMP | NDPI_PROTOCOL_NATPMP (312) | 5351 | [NATPMP.md](NATPMP.md) |

### 网络管理和监控协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| COLLECTD | NDPI_PROTOCOL_COLLECTD (298) | 25826 | [COLLECTD.md](COLLECTD.md) |
| MikroTik | NDPI_PROTOCOL_MIKROTIK (437) | 5678 | [MIKROTIK.md](MIKROTIK.md) |
| UBNTAC2 | NDPI_PROTOCOL_UBNTAC2 (31) | 10001 | [UBNTAC2.md](UBNTAC2.md) |

### 服务发现协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| SSDP | NDPI_PROTOCOL_SSDP (12) | 1900 | [SSDP.md](SSDP.md) |
| TiVoConnect | NDPI_PROTOCOL_TIVOCONNECT (308) | 2190 | [TIVOCONNECT.md](TIVOCONNECT.md) |

### VPN 协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| SoftEther | NDPI_PROTOCOL_SOFTETHER (290) | 443, 多种 | [SOFTETHER.md](SOFTETHER.md) |

### 即时通讯协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| Discord | NDPI_PROTOCOL_DISCORD (58) | 443, 多种 | [DISCORD.md](DISCORD.md) |

### 简单文件传输协议

| 协议 | 协议 ID | 端口 | 文档 |
|------|---------|------|------|
| TFTP | NDPI_PROTOCOL_TFTP (96) | 69 | [TFTP.md](TFTP.md) |
| RSH | NDPI_PROTOCOL_RSH (294) | 514 | [RSH.md](RSH.md) |

## 文档结构

每个协议文档包含以下部分：

1. **协议概述** - 协议基本信息和描述
2. **JSON 输出格式** - 完整的 JSON 结构示例
3. **字段说明表格** - 详细的字段类型和含义
4. **实际示例** - 真实场景的输出案例
5. **注意事项** - 特殊情况和限制说明
6. **相关函数** - nDPI 相关 API 函数
7. **参考资料** - RFC 和相关文档链接

## 快速查找

### 按协议名称

- [BitTorrent](BitTorrent.md)
- [COLLECTD](COLLECTD.md)
- [DHCP](DHCP.md)
- [Discord](DISCORD.md)
- [DNS](DNS.md)
- [DTLS](DTLS.md)
- [FTP](FTP.md)
- [HTTP](HTTP.md)
- [IMAP](IMAP.md)
- [Kerberos](Kerberos.md)
- [MDNS](MDNS.md)
- [MikroTik](MIKROTIK.md)
- [MongoDB](MongoDB.md)
- [NATPMP](NATPMP.md)
- [NTP](NTP.md)
- [POP3](POP3.md)
- [QUIC](QUIC.md)
- [RSH](RSH.md)
- [SIP](SIP.md)
- [SMTP](SMTP.md)
- [SNMP](SNMP.md)
- [SoftEther](SOFTETHER.md)
- [SSDP](SSDP.md)
- [SSH](SSH.md)
- [STUN](STUN.md)
- [Telnet](Telnet.md)
- [TFTP](TFTP.md)
- [TiVoConnect](TIVOCONNECT.md)
- [TLS](TLS.md)
- [UBNTAC2](UBNTAC2.md)

### 按用途

**Web 相关**
- [HTTP](HTTP.md) - HTTP/HTTPS 流量
- [TLS](TLS.md) - TLS/SSL 加密
- [QUIC](QUIC.md) - HTTP/3 和 QUIC

**邮件服务**
- [SMTP](SMTP.md) - 发送邮件
- [IMAP](IMAP.md) - 接收邮件（保留服务器副本）
- [POP3](POP3.md) - 接收邮件（下载到本地）

**域名解析**
- [DNS](DNS.md) - 标准域名解析
- [MDNS](MDNS.md) - 局域网服务发现

**网络管理**
- [SNMP](SNMP.md) - 网络设备管理
- [NTP](NTP.md) - 时间同步
- [DHCP](DHCP.md) - IP 地址分配

**安全认证**
- [SSH](SSH.md) - 安全远程登录
- [Kerberos](Kerberos.md) - 网络认证
- [TLS](TLS.md) - 传输层加密
- [DTLS](DTLS.md) - 数据报加密

**实时通信**
- [SIP](SIP.md) - VoIP 信令
- [STUN](STUN.md) - NAT 穿越

## 未文档化协议

nDPI 支持 469 个协议，但只有 30 个协议有详细的 JSON 序列化文档。查看完整的未文档化协议列表：

- **[未文档化协议列表](UNDOCUMENTED_PROTOCOLS.md)** - 437 个仅识别但无详细 JSON 输出的协议

未文档化的协议在 JSON 输出中仅包含协议名称，不包含协议特定字段。

## 源代码参考

- **主函数**: `src/lib/ndpi_utils.c` 中的 `ndpi_dpi2json()` (行 1530-2016)
- **协议 ID**: `src/include/ndpi_protocol_ids.h`
- **API 声明**: `src/include/ndpi_api.h`
- **协议检测器**: `src/lib/protocols/*.c`

## 贡献

如果您发现文档有误或需要补充，欢迎提交 Issue 或 Pull Request。

## 许可证

本文档遵循 nDPI 项目的许可证（LGPL-3.0）。
