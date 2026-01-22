# DTLS 协议文档

## 协议概述

- **协议全称**: Datagram Transport Layer Security (数据报传输层安全协议)
- **协议 ID**: NDPI_PROTOCOL_DTLS (30)
- **协议类别**: 安全加密协议
- **传输层**: UDP (多种端口)

## 简介

DTLS 是 TLS 协议的数据报版本，专为 UDP 等不可靠传输协议设计。它提供与 TLS 类似的安全保障，同时保持了 UDP 的低延迟特性。DTLS 广泛应用于 WebRTC、VoIP、VPN 等实时通信场景。

## JSON 输出格式

### 完整结构

DTLS 使用与 TLS 相同的序列化函数，输出格式基本一致：

```json
{
  "ndpi": {
    "protocol": "DTLS",
    "hostname": "webrtc.example.com",
    "tls": {
      "version": "DTLS 1.2",
      "server_names": "webrtc.example.com",
      "notbefore": "2024-01-01 00:00:00",
      "notafter": "2025-01-01 00:00:00",
      "ja3s": "eb1d94daa7e0344597e756a1fb6e7054",
      "ja4": "d12d1516h2_5b57614c22b0_3d5424432f57",
      "unsafe_cipher": 0,
      "cipher": "TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256",
      "issuerDN": "C=US, O=Example CA, CN=Example CA Root",
      "subjectDN": "CN=webrtc.example.com"
    }
  }
}
```

### 字段说明

字段说明与 TLS 协议相同，请参考 [TLS.md](TLS.md#字段说明)。

主要区别：
- `protocol` 字段值为 "DTLS"
- `version` 显示 DTLS 版本号

### DTLS 版本

| 版本 | 十六进制 | 对应 TLS 版本 | 说明 |
|------|----------|---------------|------|
| DTLS 1.0 | 0xFEFF | TLS 1.1 | 基于 TLS 1.1 |
| DTLS 1.2 | 0xFEFD | TLS 1.2 | 基于 TLS 1.2，当前主流 |
| DTLS 1.3 | 0xFEFC | TLS 1.3 | 基于 TLS 1.3，最新版本 |

注意：DTLS 版本号使用降序编号（0xFEFF, 0xFEFD...），与 TLS 不同。

## 实际示例

### 示例 1: WebRTC DTLS 握手

```json
{
  "ndpi": {
    "protocol": "DTLS",
    "tls": {
      "version": "DTLS 1.2",
      "unsafe_cipher": 0,
      "cipher": "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256",
      "fingerprint": "AA:BB:CC:DD:EE:FF:00:11:22:33:44:55:66:77:88:99:AA:BB:CC:DD"
    }
  }
}
```

### 示例 2: VPN DTLS 连接

```json
{
  "ndpi": {
    "protocol": "DTLS",
    "hostname": "vpn.company.com",
    "tls": {
      "version": "DTLS 1.2",
      "server_names": "vpn.company.com",
      "notbefore": "2024-01-01 00:00:00",
      "notafter": "2025-12-31 23:59:59",
      "unsafe_cipher": 0,
      "cipher": "TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384",
      "issuerDN": "C=US, O=Company Inc, CN=Company CA",
      "subjectDN": "C=US, O=Company Inc, CN=vpn.company.com"
    }
  }
}
```

### 示例 3: DTLS 1.3 连接

```json
{
  "ndpi": {
    "protocol": "DTLS",
    "tls": {
      "version": "DTLS 1.3",
      "unsafe_cipher": 0,
      "cipher": "TLS_AES_128_GCM_SHA256"
    }
  }
}
```

## 注意事项

### 1. DTLS vs TLS

**主要区别**：
- TLS 使用 TCP，DTLS 使用 UDP
- DTLS 增加了数据包序列号和重传机制
- DTLS 握手消息可能分片和重组
- DTLS 对数据包丢失和乱序更加宽容

### 2. 应用场景

DTLS 主要用于：
- WebRTC 数据通道加密
- VoIP 语音加密 (SRTP over DTLS)
- VPN 连接 (如 OpenConnect, Cisco AnyConnect)
- CoAP (Constrained Application Protocol)
- 物联网设备通信

### 3. 握手过程

DTLS 握手类似 TLS，但包含额外的防重放机制：
1. ClientHello (带 cookie 扩展)
2. HelloVerifyRequest (服务器验证)
3. ClientHello (包含 cookie)
4. 剩余握手消息

### 4. 与 STUN/TURN 的关系

- DTLS 常与 STUN/TURN 协议结合使用
- WebRTC 使用 DTLS-SRTP 加密媒体流
- ICE 框架中 DTLS 用于建立安全通道

### 5. 证书指纹

WebRTC 中常使用证书指纹（Certificate Fingerprint）：
- 在 SDP 中交换指纹
- 验证 DTLS 连接的对端身份
- 避免中间人攻击

### 6. SRTP 密钥导出

DTLS 握手后可导出密钥用于 SRTP：
- 使用 TLS Exporter 机制
- 导出的密钥用于加密 RTP/RTCP 流
- 保证端到端媒体加密

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_DTLS, 行 1998-2003)
- **协议检测**: `src/lib/protocols/dtls.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_DTLS:
  ndpi_tls2json(ndpi_struct, serializer, flow, false);
  #ifdef CUSTOM_NDPI_PROTOCOLS
  #include "../../../nDPI-custom/ndpi_utils_dpi2json_dtls.c"
  #endif
  break;
```

注意：DTLS 使用与 TLS 相同的 `ndpi_tls2json()` 函数，但 `is_tls_proto` 参数为 false。

## 相关函数

### nDPI 检测函数

- `ndpi_search_dtls()` - DTLS 协议检测器
- `ndpi_tls2json()` - TLS/DTLS 信息序列化

## 参考资料

- [RFC 6347 - Datagram Transport Layer Security Version 1.2](https://tools.ietf.org/html/rfc6347)
- [RFC 9147 - The Datagram Transport Layer Security (DTLS) Protocol Version 1.3](https://tools.ietf.org/html/rfc9147)
- [RFC 5764 - DTLS Extension to Establish Keys for SRTP](https://tools.ietf.org/html/rfc5764)
- [WebRTC Security Architecture](https://tools.ietf.org/html/rfc8827)
- [nDPI DTLS 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/dtls.c)
- [TLS 协议文档](TLS.md) - 相关 TLS 信息

## 常见用例

### 1. WebRTC 通信

保护浏览器间的实时音视频和数据通道。

### 2. VoIP 安全

加密 SIP/RTP 语音通话流量。

### 3. VPN 连接

某些 VPN 解决方案使用 DTLS 提供低延迟加密。

### 4. IoT 设备通信

物联网设备间的安全数据传输。

### 5. 游戏服务器

实时多人游戏的安全通信。

### 6. 流媒体

低延迟视频流的加密传输。

## DTLS 与其他协议的关系

- **STUN/TURN**: 与 DTLS 结合用于 NAT 穿越
- **SRTP**: DTLS 派生密钥用于 SRTP 加密
- **SCTP**: DTLS 可以加密 SCTP over UDP
- **QUIC**: QUIC 内置类似 DTLS 的加密机制（基于 TLS 1.3）

参见：
- [STUN.md](STUN.md)
- [QUIC.md](QUIC.md)
- [SIP.md](SIP.md)
