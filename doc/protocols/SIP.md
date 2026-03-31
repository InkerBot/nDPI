# SIP 协议文档

## 协议概述

- **协议全称**: Session Initiation Protocol (会话初始协议)
- **协议 ID**: NDPI_PROTOCOL_SIP (100)
- **协议类别**: VoIP 信令协议
- **传输层**: UDP/TCP (端口 5060, 5061)

## 简介

SIP 是一种信令协议，用于发起、管理和终止实时会话，包括语音、视频和即时消息通信。它是 VoIP 通信的核心协议之一，被广泛应用于企业和运营商网络。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "SIP",
    "sip": {
      "from": "sip:alice@atlanta.example.com",
      "from_imsi": "123456789012345",
      "to": "sip:bob@biloxi.example.com",
      "to_imsi": "543210987654321"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| from | string | 呼叫发起方 SIP URI | "sip:alice@atlanta.example.com" |
| from_imsi | string | 发起方 IMSI (国际移动用户识别码) | "123456789012345" |
| to | string | 呼叫接收方 SIP URI | "sip:bob@biloxi.example.com" |
| to_imsi | string | 接收方 IMSI | "543210987654321" |

### SIP URI 格式

SIP URI 遵循以下格式：
```
sip:user@domain[:port][;parameters]
sips:user@domain[:port][;parameters]  (secure SIP)
```

示例：
- `sip:alice@example.com`
- `sip:+15551234567@voip.provider.com`
- `sips:secure-user@secure-domain.com:5061`

## 实际示例

### 示例 1: 标准 SIP 呼叫

```json
{
  "ndpi": {
    "protocol": "SIP",
    "sip": {
      "from": "sip:john.doe@company.com",
      "to": "sip:jane.smith@company.com"
    }
  }
}
```

### 示例 2: 移动网络 SIP（带 IMSI）

```json
{
  "ndpi": {
    "protocol": "SIP",
    "sip": {
      "from": "sip:+86138****1234@ims.carrier.com",
      "from_imsi": "460001234567890",
      "to": "sip:+86139****5678@ims.carrier.com",
      "to_imsi": "460009876543210"
    }
  }
}
```

### 示例 3: 企业 VoIP 系统

```json
{
  "ndpi": {
    "protocol": "SIP",
    "sip": {
      "from": "sip:1001@pbx.enterprise.org",
      "to": "sip:1002@pbx.enterprise.org"
    }
  }
}
```

### 示例 4: SIP Trunk 连接

```json
{
  "ndpi": {
    "protocol": "SIP",
    "sip": {
      "from": "sip:+15551234567@sip-trunk.provider.com",
      "to": "sip:+15559876543@pstn-gateway.com"
    }
  }
}
```

## 注意事项

### 1. SIP 端口

| 端口 | 协议 | 用途 |
|------|------|------|
| 5060 | UDP/TCP | 标准 SIP |
| 5061 | TCP/TLS | 安全 SIP (SIPS) |

### 2. SIP 请求方法

常见的 SIP 方法：
- **INVITE** - 发起会话
- **ACK** - 确认请求
- **BYE** - 终止会话
- **CANCEL** - 取消请求
- **REGISTER** - 注册用户
- **OPTIONS** - 查询能力
- **INFO** - 发送会话内信息
- **REFER** - 呼叫转移
- **SUBSCRIBE** - 订阅事件
- **NOTIFY** - 事件通知

### 3. SIP 响应码

| 类别 | 范围 | 含义 |
|------|------|------|
| 1xx | 100-199 | 临时响应（呼叫正在进行） |
| 2xx | 200-299 | 成功响应 |
| 3xx | 300-399 | 重定向响应 |
| 4xx | 400-499 | 客户端错误 |
| 5xx | 500-599 | 服务器错误 |
| 6xx | 600-699 | 全局失败 |

**常见响应码**：
- 100 Trying
- 180 Ringing
- 200 OK
- 403 Forbidden
- 404 Not Found
- 486 Busy Here
- 503 Service Unavailable

### 4. SIP 头部

重要的 SIP 头部字段：
- **From** - 发起方标识
- **To** - 目标方标识
- **Call-ID** - 唯一呼叫标识符
- **CSeq** - 命令序列号
- **Via** - 路由路径
- **Contact** - 联系地址
- **User-Agent** - 客户端信息
- **Allow** - 支持的方法

### 5. IMSI 字段

- IMSI (International Mobile Subscriber Identity)
- 用于移动网络中识别用户
- 15 位数字
- 仅在 IMS (IP Multimedia Subsystem) 网络中出现

### 6. SIP 与 RTP

- SIP 用于呼叫信令（建立、修改、终止会话）
- RTP (Real-time Transport Protocol) 用于传输实际的音视频流
- SDP (Session Description Protocol) 在 SIP 消息中协商媒体参数

### 7. 安全性

**SIPS (Secure SIP)**：
- 使用 TLS 加密
- 端口 5061
- URI 前缀为 `sips:`

**其他安全机制**：
- SIP over TLS
- SIP over IPsec
- SRTP (Secure RTP) 用于媒体加密

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_SIP, 行 1981-1992)
- **协议检测**: `src/lib/protocols/sip.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_SIP:
  ndpi_serialize_start_of_block(serializer, "sip");
  if(flow->protos.sip.from)
    ndpi_serialize_string_string(serializer, "from", flow->protos.sip.from);
  if(flow->protos.sip.from_imsi[0] != '\0')
    ndpi_serialize_string_string(serializer, "from_imsi", flow->protos.sip.from_imsi);
  if(flow->protos.sip.to)
    ndpi_serialize_string_string(serializer, "to", flow->protos.sip.to);
  if(flow->protos.sip.to_imsi[0] != '\0')
    ndpi_serialize_string_string(serializer, "to_imsi", flow->protos.sip.to_imsi);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_sip()` - SIP 协议检测器

## 参考资料

- [RFC 3261 - SIP: Session Initiation Protocol](https://tools.ietf.org/html/rfc3261)
- [RFC 3262 - Reliability of Provisional Responses in SIP](https://tools.ietf.org/html/rfc3262)
- [RFC 3263 - SIP: Locating SIP Servers](https://tools.ietf.org/html/rfc3263)
- [RFC 3264 - An Offer/Answer Model with SDP](https://tools.ietf.org/html/rfc3264)
- [RFC 3311 - The SIP UPDATE Method](https://tools.ietf.org/html/rfc3311)
- [nDPI SIP 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/sip.c)

## 常见用例

### 1. IP 电话系统

企业 IP-PBX 系统使用 SIP 进行内外部通话。

### 2. 移动 VoLTE

4G/5G 网络使用 IMS/SIP 实现语音通话。

### 3. 视频会议

WebRTC、Zoom 等使用 SIP 或类似协议。

### 4. 即时消息

SIP 支持即时消息传递（SIP MESSAGE）。

### 5. 在线状态

SIP SUBSCRIBE/NOTIFY 实现在线状态功能。

### 6. SIP Trunking

连接企业 PBX 到运营商网络。

### 7. 呼叫中心

大规模呼叫中心系统基于 SIP。

## SIP 架构组件

- **User Agent (UA)** - SIP 客户端
  - UAC (User Agent Client) - 发起请求
  - UAS (User Agent Server) - 响应请求
- **Proxy Server** - 路由 SIP 消息
- **Registrar** - 注册用户位置
- **Redirect Server** - 返回重定向信息
- **Back-to-Back User Agent (B2BUA)** - 充当中介

## SIP 呼叫流程示例

典型的 SIP 呼叫建立流程：

```
Alice                Proxy               Bob
  |                    |                  |
  |-- INVITE --------->|                  |
  |<-- 100 Trying -----|                  |
  |                    |-- INVITE ------->|
  |                    |<-- 180 Ringing --|
  |<-- 180 Ringing ----|                  |
  |                    |<-- 200 OK -------|
  |<-- 200 OK ---------|                  |
  |-- ACK ------------>|-- ACK ---------->|
  |                    |                  |
  |<======= RTP Media Stream ============>|
  |                    |                  |
  |-- BYE ------------>|-- BYE ---------->|
  |<-- 200 OK ---------|<-- 200 OK -------|
```

## 监控和故障排查

监控 SIP 流量的关键指标：
- 呼叫建立成功率
- 呼叫建立时间
- SIP 响应码分布
- 注册失败率
- 媒体质量指标

## 相关协议

- [STUN.md](STUN.md) - NAT 穿越
- [RTP](https://tools.ietf.org/html/rfc3550) - 媒体传输
- [RTCP](https://tools.ietf.org/html/rfc3550) - 媒体控制
- [SDP](https://tools.ietf.org/html/rfc4566) - 会话描述
- [DTLS.md](DTLS.md) - WebRTC 中的加密
