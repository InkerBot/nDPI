# TLS 协议文档

## 协议概述

- **协议全称**: Transport Layer Security (传输层安全协议)
- **协议 ID**: NDPI_PROTOCOL_TLS (91)
- **协议类别**: 安全加密协议
- **传输层**: TCP (默认端口 443)

## 简介

TLS 是用于在网络通信中提供安全性和数据完整性的加密协议。它是 SSL (Secure Sockets Layer) 的后继者，广泛应用于 HTTPS、FTPS、SMTPS 等安全通信场景。nDPI 可以从 TLS 握手中提取丰富的元数据信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "TLS",
    "hostname": "www.example.com",
    "tls": {
      "version": "TLS 1.3",
      "server_names": "www.example.com",
      "notbefore": "2024-01-01 00:00:00",
      "notafter": "2025-01-01 00:00:00",
      "ja3s": "eb1d94daa7e0344597e756a1fb6e7054",
      "ja4": "t13d1517h2_5b57614c22b0_3d5424432f57",
      "unsafe_cipher": 0,
      "cipher": "TLS_AES_128_GCM_SHA256",
      "issuerDN": "C=US, O=DigiCert Inc, CN=DigiCert TLS RSA SHA256 2020 CA1",
      "subjectDN": "C=US, ST=California, L=Los Angeles, O=Example Inc, CN=www.example.com",
      "advertised_alpns": "h2,http/1.1",
      "negotiated_alpn": "h2",
      "tls_supported_versions": "TLS 1.3,TLS 1.2",
      "fingerprint": "AA:BB:CC:DD:EE:FF:00:11:22:33:44:55:66:77:88:99:AA:BB:CC:DD"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| version | string | TLS 协议版本 | "TLS 1.3", "TLS 1.2" |
| server_names | string | SNI 服务器名称 | "www.example.com" |
| notbefore | string | 证书生效时间 | "2024-01-01 00:00:00" |
| notafter | string | 证书过期时间 | "2025-01-01 00:00:00" |
| ja3s | string | JA3S 服务器指纹 | "eb1d94daa7e0344597e756a1fb6e7054" |
| ja4 | string | JA4 客户端指纹 | "t13d1517h2_5b57614c22b0_3d5424432f57" |
| unsafe_cipher | uint32 | 是否使用不安全密码套件 (0=安全, 1=不安全) | 0 |
| cipher | string | 协商的密码套件 | "TLS_AES_128_GCM_SHA256" |
| issuerDN | string | 证书颁发者 DN | "C=US, O=DigiCert Inc, CN=..." |
| subjectDN | string | 证书主题 DN | "C=US, ST=CA, L=LA, O=..., CN=..." |
| advertised_alpns | string | 客户端通告的 ALPN 列表 | "h2,http/1.1" |
| negotiated_alpn | string | 协商的 ALPN 协议 | "h2" |
| tls_supported_versions | string | 支持的 TLS 版本列表 | "TLS 1.3,TLS 1.2" |
| fingerprint | string | 证书 SHA1 指纹 | "AA:BB:CC:DD:..." |

### TLS 版本

| 版本 | 十六进制 | 说明 |
|------|----------|------|
| SSL 3.0 | 0x0300 | 已弃用，存在安全漏洞 |
| TLS 1.0 | 0x0301 | 已弃用，不再推荐使用 |
| TLS 1.1 | 0x0302 | 已弃用，不再推荐使用 |
| TLS 1.2 | 0x0303 | 广泛使用，安全可靠 |
| TLS 1.3 | 0x0304 | 最新版本，性能更好 |

### 常见密码套件

#### TLS 1.3 密码套件
- TLS_AES_128_GCM_SHA256
- TLS_AES_256_GCM_SHA384
- TLS_CHACHA20_POLY1305_SHA256

#### TLS 1.2 密码套件
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

### ALPN (Application-Layer Protocol Negotiation)

常见的 ALPN 协议标识：
- `h2` - HTTP/2
- `h3` - HTTP/3
- `http/1.1` - HTTP/1.1
- `h2c` - HTTP/2 over cleartext

## 实际示例

### 示例 1: HTTPS 连接 (TLS 1.3)

```json
{
  "ndpi": {
    "protocol": "TLS",
    "hostname": "github.com",
    "tls": {
      "version": "TLS 1.3",
      "server_names": "github.com",
      "notbefore": "2024-03-25 00:00:00",
      "notafter": "2025-04-25 23:59:59",
      "ja3s": "eb1d94daa7e0344597e756a1fb6e7054",
      "ja4": "t13d1517h2_5b57614c22b0_3d5424432f57",
      "unsafe_cipher": 0,
      "cipher": "TLS_AES_128_GCM_SHA256",
      "issuerDN": "C=US, O=DigiCert Inc, CN=DigiCert TLS Hybrid ECC SHA384 2020 CA1",
      "subjectDN": "C=US, ST=California, L=San Francisco, O=GitHub Inc, CN=github.com",
      "advertised_alpns": "h2,http/1.1",
      "negotiated_alpn": "h2",
      "tls_supported_versions": "TLS 1.3,TLS 1.2",
      "fingerprint": "B5:61:EB:74:E5:7D:4E:80:01:69:23:15:F2:C4:E1:A7:CD:0E:49:53"
    }
  }
}
```

### 示例 2: TLS 1.2 连接

```json
{
  "ndpi": {
    "protocol": "TLS",
    "hostname": "www.cloudflare.com",
    "tls": {
      "version": "TLS 1.2",
      "server_names": "www.cloudflare.com",
      "notbefore": "2024-01-15 00:00:00",
      "notafter": "2025-02-15 23:59:59",
      "ja3s": "ae4edc6faf64d08308082ad26be31b51",
      "ja4": "t12d1516h2_bbd16e265919_02ca6da32ddf",
      "unsafe_cipher": 0,
      "cipher": "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256",
      "issuerDN": "C=US, O=Cloudflare Inc, CN=Cloudflare Inc ECC CA-3",
      "subjectDN": "C=US, ST=California, L=San Francisco, O=Cloudflare Inc, CN=www.cloudflare.com",
      "advertised_alpns": "h2,http/1.1",
      "negotiated_alpn": "h2"
    }
  }
}
```

### 示例 3: 使用不安全密码套件

```json
{
  "ndpi": {
    "protocol": "TLS",
    "hostname": "old-server.example.com",
    "tls": {
      "version": "TLS 1.0",
      "server_names": "old-server.example.com",
      "unsafe_cipher": 1,
      "cipher": "TLS_RSA_WITH_RC4_128_SHA",
      "issuerDN": "C=US, O=Old CA, CN=Old CA Root",
      "subjectDN": "CN=old-server.example.com"
    }
  }
}
```

### 示例 4: 自签名证书

```json
{
  "ndpi": {
    "protocol": "TLS",
    "hostname": "localhost",
    "tls": {
      "version": "TLS 1.3",
      "server_names": "localhost",
      "notbefore": "2024-01-01 00:00:00",
      "notafter": "2034-01-01 00:00:00",
      "unsafe_cipher": 0,
      "cipher": "TLS_AES_256_GCM_SHA384",
      "issuerDN": "CN=localhost",
      "subjectDN": "CN=localhost"
    }
  }
}
```

## 注意事项

### 1. SNI (Server Name Indication)

- SNI 在 TLS 握手的 ClientHello 消息中发送
- 允许服务器在同一 IP 地址上托管多个证书
- `server_names` 字段从 SNI 扩展中提取

### 2. JA3/JA3S 指纹

- **JA3**: 客户端 TLS 指纹，用于识别客户端应用
- **JA3S**: 服务器 TLS 指纹，用于识别服务器配置
- 可用于威胁检测和应用识别

### 3. JA4 指纹

- JA4 是 JA3 的改进版本
- 提供更好的稳定性和准确性
- 包含更多 TLS 握手信息

### 4. 证书有效期

- `notbefore` 和 `notafter` 从 X.509 证书中提取
- 格式：YYYY-MM-DD HH:MM:SS (UTC)
- 用于检测证书是否过期

### 5. 不安全密码套件

`unsafe_cipher` 标记使用的密码套件是否安全：
- 弱加密算法（DES, RC4）
- 弱密钥长度（< 128 位）
- 不提供前向保密的套件

### 6. ALPN 协商

- 用于在 TLS 握手中协商应用层协议
- 避免额外的往返延迟
- HTTP/2 和 HTTP/3 依赖 ALPN

### 7. 证书链信息

- `issuerDN`: 证书颁发者
- `subjectDN`: 证书持有者
- DN (Distinguished Name) 包含国家、组织、通用名称等

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (ndpi_tls2json 函数, 行 1391-1466)
- **协议检测**: `src/lib/protocols/tls.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_TLS:
  ndpi_tls2json(ndpi_struct, serializer, flow, true);
  break;

static void ndpi_tls2json(struct ndpi_detection_module_struct *ndpi_struct,
                         ndpi_serializer *serializer,
                         struct ndpi_flow_struct *flow, bool is_tls_proto) {
  // 提取 TLS 版本、密码套件、证书信息等
  // 序列化 JA3/JA3S/JA4 指纹
  // 序列化 ALPN、SNI 等扩展信息
}
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_tls()` - TLS 协议检测器
- `ndpi_tls2json()` - TLS 信息序列化
- `ndpi_ssl_version2str()` - TLS 版本转字符串
- `ndpi_cipher2str()` - 密码套件转字符串

### TLS 指纹

- `ndpi_compute_ja3()` - 计算 JA3 指纹
- `ndpi_compute_ja3s()` - 计算 JA3S 指纹
- `ndpi_compute_ja4()` - 计算 JA4 指纹

## 参考资料

- [RFC 8446 - The Transport Layer Security (TLS) Protocol Version 1.3](https://tools.ietf.org/html/rfc8446)
- [RFC 5246 - The TLS Protocol Version 1.2](https://tools.ietf.org/html/rfc5246)
- [RFC 6066 - TLS Extensions](https://tools.ietf.org/html/rfc6066)
- [RFC 7301 - ALPN Extension](https://tools.ietf.org/html/rfc7301)
- [JA3: TLS Client Fingerprinting](https://github.com/salesforce/ja3)
- [JA4 Fingerprinting](https://github.com/FoxIO-LLC/ja4)
- [nDPI TLS 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/tls.c)

## 常见用例

### 1. HTTPS 流量分析

分析加密 Web 流量的目标服务器和应用。

### 2. 威胁检测

使用 JA3/JA3S 指纹识别恶意软件和 C2 通信。

### 3. 应用识别

基于 SNI 和 ALPN 识别具体应用和服务。

### 4. 安全审计

检测使用过时 TLS 版本和不安全密码套件的连接。

### 5. 证书监控

监控证书有效期，及时更新即将过期的证书。

### 6. 性能优化

分析 TLS 握手性能，优化密码套件选择。

### 7. 合规性检查

确保所有 TLS 连接符合安全策略要求（TLS 1.2+）。

## 相关协议

- [DTLS.md](DTLS.md) - 数据报 TLS，用于 UDP
- [QUIC.md](QUIC.md) - 内置 TLS 1.3 的传输协议
- [HTTP.md](HTTP.md) - 明文 HTTP
