# QUIC 协议文档

## 协议概述

- **协议全称**: Quick UDP Internet Connections (快速 UDP 互联网连接)
- **协议 ID**: NDPI_PROTOCOL_QUIC (188)
- **协议类别**: 传输层协议
- **传输层**: UDP (默认端口 443)

## 简介

QUIC 是 Google 开发的基于 UDP 的传输层协议，旨在取代 TCP+TLS，提供更快的连接建立和更好的拥塞控制。QUIC 已成为 HTTP/3 的基础传输协议。nDPI 可以提取 QUIC 版本和内嵌的 TLS 信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "QUIC",
    "hostname": "www.google.com",
    "quic": {
      "quic_version": "Q050",
      "tls": {
        "version": "TLS 1.3",
        "server_names": "www.google.com",
        "ja3s": "eb1d94daa7e0344597e756a1fb6e7054",
        "ja4": "q13d0312h3_9dc949149365_e95bd914a135",
        "unsafe_cipher": 0,
        "cipher": "TLS_AES_128_GCM_SHA256",
        "advertised_alpns": "h3",
        "negotiated_alpn": "h3"
      }
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| quic_version | string | QUIC 协议版本 | "Q050", "0x00000001" |
| tls | object | 内嵌的 TLS 1.3 信息 | 见 TLS 字段说明 |

TLS 相关字段说明请参考 [TLS.md](TLS.md#字段说明)。

### QUIC 版本

QUIC 有多个版本，主要分为：

**Google QUIC (gQUIC)**：
- Q043, Q046, Q050 - Google 早期版本
- 使用专有版本号

**IETF QUIC**：
- 0x00000001 (v1) - IETF 标准版本 (RFC 9000)
- 0xff00001d (draft-29) - 草案版本
- 使用 RFC 定义的版本号

### ALPN 与 HTTP/3

QUIC 通常与 HTTP/3 配合使用：
- `h3` - HTTP/3
- `h3-29`, `h3-27` - HTTP/3 草案版本

## 实际示例

### 示例 1: HTTP/3 over QUIC (Google)

```json
{
  "ndpi": {
    "protocol": "QUIC",
    "hostname": "www.google.com",
    "quic": {
      "quic_version": "Q050",
      "tls": {
        "version": "TLS 1.3",
        "server_names": "www.google.com",
        "ja4": "q13d0312h3_9dc949149365_e95bd914a135",
        "unsafe_cipher": 0,
        "cipher": "TLS_AES_128_GCM_SHA256",
        "advertised_alpns": "h3-Q050,h3-Q046,h3-Q043",
        "negotiated_alpn": "h3-Q050"
      }
    }
  }
}
```

### 示例 2: IETF QUIC v1

```json
{
  "ndpi": {
    "protocol": "QUIC",
    "hostname": "cloudflare.com",
    "quic": {
      "quic_version": "0x00000001",
      "tls": {
        "version": "TLS 1.3",
        "server_names": "cloudflare.com",
        "unsafe_cipher": 0,
        "cipher": "TLS_AES_256_GCM_SHA384",
        "advertised_alpns": "h3",
        "negotiated_alpn": "h3"
      }
    }
  }
}
```

### 示例 3: QUIC with Certificate

```json
{
  "ndpi": {
    "protocol": "QUIC",
    "hostname": "www.youtube.com",
    "quic": {
      "quic_version": "Q050",
      "tls": {
        "version": "TLS 1.3",
        "server_names": "www.youtube.com",
        "notbefore": "2024-01-15 08:00:00",
        "notafter": "2024-04-08 07:59:59",
        "unsafe_cipher": 0,
        "cipher": "TLS_AES_128_GCM_SHA256",
        "issuerDN": "C=US, O=Google Trust Services, CN=GTS CA 1C3",
        "subjectDN": "CN=*.youtube.com",
        "advertised_alpns": "h3",
        "negotiated_alpn": "h3"
      }
    }
  }
}
```

### 示例 4: QUIC Draft Version

```json
{
  "ndpi": {
    "protocol": "QUIC",
    "hostname": "example.com",
    "quic": {
      "quic_version": "0xff00001d",
      "tls": {
        "version": "TLS 1.3",
        "server_names": "example.com",
        "unsafe_cipher": 0,
        "cipher": "TLS_CHACHA20_POLY1305_SHA256",
        "advertised_alpns": "h3-29",
        "negotiated_alpn": "h3-29"
      }
    }
  }
}
```

## 注意事项

### 1. QUIC vs TCP+TLS

**QUIC 优势**：
- 0-RTT 连接建立（无需往返）
- 更好的拥塞控制
- 连接迁移（IP 地址变化时保持连接）
- 消除队头阻塞
- 内置加密（TLS 1.3）

### 2. QUIC 与 HTTP/3

- HTTP/3 完全基于 QUIC
- HTTP/2 使用 TCP+TLS
- QUIC 替代了 TCP 和 TLS 层

### 3. 版本协商

- QUIC 连接开始时进行版本协商
- 客户端提议版本，服务器接受或拒绝
- 版本不匹配时服务器返回支持的版本列表

### 4. 0-RTT 连接

QUIC 支持 0-RTT 数据传输：
- 使用之前缓存的会话密钥
- 首个数据包即可携带应用数据
- 显著减少连接延迟

### 5. 端口使用

- 通常使用 UDP 443 端口
- 与 HTTPS 共享端口号
- 通过 HTTP Alt-Svc 头部发现

### 6. 连接 ID

QUIC 使用连接 ID 而非 IP+端口：
- 支持连接迁移
- 客户端 IP 变化时保持连接
- 对移动设备友好

### 7. 多路复用

QUIC 原生支持流多路复用：
- 单个连接承载多个流
- 流之间相互独立
- 避免队头阻塞

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_QUIC, 行 1737-1747)
- **协议检测**: `src/lib/protocols/quic.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_QUIC:
  ndpi_serialize_start_of_block(serializer, "quic");

  ndpi_quic_version2str(quic_version, sizeof(quic_version),
                        flow->protos.tls_quic.quic_version);
  ndpi_serialize_string_string(serializer, "quic_version", quic_version);

  ndpi_tls2json(ndpi_struct, serializer, flow, false);

  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_quic()` - QUIC 协议检测器
- `ndpi_quic_version2str()` - QUIC 版本转字符串
- `ndpi_tls2json()` - TLS 信息序列化

## 参考资料

- [RFC 9000 - QUIC: A UDP-Based Multiplexed and Secure Transport](https://tools.ietf.org/html/rfc9000)
- [RFC 9001 - Using TLS to Secure QUIC](https://tools.ietf.org/html/rfc9001)
- [RFC 9114 - HTTP/3](https://tools.ietf.org/html/rfc9114)
- [QUIC Working Group](https://quicwg.org/)
- [Chromium QUIC Implementation](https://www.chromium.org/quic)
- [nDPI QUIC 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/quic.c)

## 常见用例

### 1. HTTP/3 Web 浏览

现代浏览器使用 QUIC/HTTP/3 访问支持的网站。

### 2. 视频流媒体

YouTube、Netflix 等使用 QUIC 提供更流畅的视频播放。

### 3. 移动应用

移动 App 使用 QUIC 在网络切换时保持连接。

### 4. CDN 服务

Cloudflare、Fastly 等 CDN 提供 QUIC 支持。

### 5. 实时通信

低延迟实时应用受益于 QUIC 的快速连接建立。

### 6. API 通信

现代 API 使用 QUIC 提高性能和可靠性。

## QUIC 采用情况

**主要支持者**：
- Google (Chrome, YouTube, Gmail)
- Cloudflare
- Facebook/Meta
- Akamai
- Fastly

**浏览器支持**：
- Chrome/Edge (Chromium) - 完整支持
- Firefox - 支持
- Safari - 部分支持

**服务器支持**：
- nginx (通过 ngx_quic 模块)
- LiteSpeed
- Caddy
- Apache (实验性)

## 相关协议

- [TLS.md](TLS.md) - QUIC 使用 TLS 1.3 加密
- [HTTP.md](HTTP.md) - HTTP/1.1 和 HTTP/2
- [STUN.md](STUN.md) - NAT 穿越
