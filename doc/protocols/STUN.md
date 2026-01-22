# STUN 协议文档

## 协议概述

- **协议全称**: Session Traversal Utilities for NAT (NAT 会话穿越实用工具)
- **协议 ID**: NDPI_PROTOCOL_STUN (78)
- **协议类别**: NAT 穿越协议
- **传输层**: UDP/TCP (端口 3478, 5349)

## 简介

STUN 是一种网络协议，允许位于 NAT 后面的客户端发现其公网 IP 地址和端口，以及确定 NAT 的类型。STUN 广泛应用于 VoIP、视频会议和 WebRTC 等实时通信场景。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "STUN",
    "stun": {
      "mapped_address": "203.0.113.5:12345",
      "peer_address": "198.51.100.10:54321",
      "relayed_address": "192.0.2.15:32768",
      "response_origin": "203.0.113.1:3478",
      "other_address": "203.0.113.2:3479",
      "multimedia_flow_types": "Audio, Video"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| mapped_address | string | 映射地址（客户端的公网 IP 和端口） | "203.0.113.5:12345" |
| peer_address | string | 对等方地址 | "198.51.100.10:54321" |
| relayed_address | string | 中继地址（TURN 服务器分配的地址） | "192.0.2.15:32768" |
| response_origin | string | 响应源地址（STUN 服务器地址） | "203.0.113.1:3478" |
| other_address | string | 备用地址（用于 NAT 类型检测） | "203.0.113.2:3479" |
| multimedia_flow_types | string | 多媒体流类型 | "Audio", "Video", "Audio, Video" |

### STUN 端口

| 端口 | 协议 | 用途 |
|------|------|------|
| 3478 | UDP/TCP | 标准 STUN |
| 5349 | TCP/TLS | STUN over TLS (STUNS) |

## 实际示例

### 示例 1: 基本 STUN 绑定请求

```json
{
  "ndpi": {
    "protocol": "STUN",
    "stun": {
      "mapped_address": "203.0.113.100:45678",
      "response_origin": "stun.example.com:3478"
    }
  }
}
```

### 示例 2: WebRTC ICE 连接

```json
{
  "ndpi": {
    "protocol": "STUN",
    "stun": {
      "mapped_address": "198.51.100.50:55123",
      "peer_address": "192.0.2.75:48976",
      "multimedia_flow_types": "Audio, Video"
    }
  }
}
```

### 示例 3: TURN 中继连接

```json
{
  "ndpi": {
    "protocol": "STUN",
    "stun": {
      "mapped_address": "203.0.113.200:33456",
      "relayed_address": "198.51.100.10:49152",
      "response_origin": "turn.example.com:3478",
      "multimedia_flow_types": "Video"
    }
  }
}
```

### 示例 4: NAT 类型检测

```json
{
  "ndpi": {
    "protocol": "STUN",
    "stun": {
      "mapped_address": "203.0.113.150:54321",
      "response_origin": "203.0.113.1:3478",
      "other_address": "203.0.113.2:3479"
    }
  }
}
```

## 注意事项

### 1. STUN vs TURN

**STUN (Session Traversal Utilities for NAT)**：
- 发现公网地址和端口
- 轻量级，带宽开销小
- 适用于大多数 NAT 场景
- 某些严格的 NAT 无法工作

**TURN (Traversal Using Relays around NAT)**：
- 中继所有数据流量
- 确保连接成功（fallback）
- 带宽开销大
- 需要专用服务器资源

### 2. ICE (Interactive Connectivity Establishment)

ICE 是综合框架，结合使用：
- STUN - 尝试直接连接
- TURN - fallback 到中继
- 本地候选 - 尝试本地网络
- 服务器反射候选 - STUN 发现的地址
- 中继候选 - TURN 分配的地址

### 3. NAT 类型

STUN 可以帮助识别 NAT 类型：
- **Full Cone NAT** - 最宽松，所有外部主机可访问
- **Restricted Cone NAT** - 限制到特定外部 IP
- **Port Restricted Cone NAT** - 限制到特定 IP 和端口
- **Symmetric NAT** - 最严格，需要 TURN

### 4. STUN 消息类型

- **Binding Request** - 请求映射地址
- **Binding Response** - 返回映射地址
- **Binding Error Response** - 错误响应
- **Shared Secret Request/Response** - 凭证交换（已弃用）

### 5. STUN 属性

常见 STUN 属性：
- **MAPPED-ADDRESS** - 映射地址
- **XOR-MAPPED-ADDRESS** - 异或映射地址（防止 ALG 修改）
- **USERNAME** - 用户名
- **MESSAGE-INTEGRITY** - 消息完整性
- **FINGERPRINT** - 指纹
- **ERROR-CODE** - 错误代码
- **REALM** - 认证域
- **NONCE** - 随机数

### 6. WebRTC 中的 STUN

WebRTC 大量使用 STUN/TURN：
- 建立 P2P 连接
- NAT 穿越
- 网络候选收集
- 连接性检查

### 7. 公共 STUN 服务器

一些公共可用的 STUN 服务器：
- stun.l.google.com:19302
- stun1.l.google.com:19302
- stun.stunprotocol.org:3478
- stun.services.mozilla.com

注意：生产环境建议使用自己的 STUN/TURN 服务器。

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_STUN, 行 1953-1979)
- **协议检测**: `src/lib/protocols/stun.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_STUN:
  ndpi_serialize_start_of_block(serializer, "stun");

  if(flow->stun.mapped_address.port)
    ndpi_serialize_string_string(serializer, "mapped_address", 
                                 print_ndpi_address_port(&flow->stun.mapped_address, buf, sizeof(buf)));

  if(flow->stun.peer_address.port)
    ndpi_serialize_string_string(serializer, "peer_address", 
                                 print_ndpi_address_port(&flow->stun.peer_address, buf, sizeof(buf)));

  if(flow->stun.relayed_address.port)
    ndpi_serialize_string_string(serializer, "relayed_address", 
                                 print_ndpi_address_port(&flow->stun.relayed_address, buf, sizeof(buf)));

  if(flow->stun.response_origin.port)
    ndpi_serialize_string_string(serializer, "response_origin", 
                                 print_ndpi_address_port(&flow->stun.response_origin, buf, sizeof(buf)));

  if(flow->stun.other_address.port)
    ndpi_serialize_string_string(serializer, "other_address", 
                                 print_ndpi_address_port(&flow->stun.other_address, buf, sizeof(buf)));

  ndpi_serialize_string_string(serializer, "multimedia_flow_types",
                               ndpi_multimedia_flowtype2str(content, sizeof(content), flow->flow_multimedia_types));

  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_stun()` - STUN 协议检测器
- `print_ndpi_address_port()` - 格式化地址和端口
- `ndpi_multimedia_flowtype2str()` - 多媒体类型转字符串

## 参考资料

- [RFC 5389 - Session Traversal Utilities for NAT (STUN)](https://tools.ietf.org/html/rfc5389)
- [RFC 5766 - Traversal Using Relays around NAT (TURN)](https://tools.ietf.org/html/rfc5766)
- [RFC 5245 - Interactive Connectivity Establishment (ICE)](https://tools.ietf.org/html/rfc5245)
- [RFC 8445 - ICE: A Protocol for NAT Traversal (ICE v2)](https://tools.ietf.org/html/rfc8445)
- [WebRTC Connectivity](https://webrtc.org/getting-started/peer-connections)
- [nDPI STUN 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/stun.c)

## 常见用例

### 1. WebRTC 连接

浏览器间的实时音视频通信。

### 2. VoIP 应用

Skype、WhatsApp、Telegram 等 VoIP 功能。

### 3. 视频会议

Zoom、Teams、Google Meet 等平台。

### 4. P2P 游戏

实时多人游戏的 P2P 连接。

### 5. 远程桌面

P2P 远程桌面连接。

### 6. 文件传输

P2P 文件传输应用。

### 7. IoT 设备

智能设备的远程访问。

## STUN/TURN 服务器

开源 STUN/TURN 服务器实现：
- **coturn** - 功能完整的 STUN/TURN 服务器
- **STUNTMAN** - Vovida Networks 的实现
- **restund** - 轻量级实现

## 连接建立流程

典型的 ICE + STUN 连接流程：

```
1. 收集候选地址
   - 本地地址（局域网 IP）
   - 服务器反射地址（STUN 发现的公网 IP）
   - 中继地址（TURN 分配的地址）

2. 交换候选地址（通过信令服务器）

3. 连接性检查
   - 尝试所有候选对
   - 测试连接性
   - 测量延迟

4. 选择最佳路径
   - 优先直接连接
   - fallback 到中继

5. 建立媒体流
```

## 性能考虑

- **STUN 延迟** - 通常 < 100ms
- **TURN 开销** - 所有流量经过服务器
- **带宽** - TURN 需要足够的上行带宽
- **服务器位置** - 地理位置影响延迟

## 安全性

- 使用 TLS (端口 5349) 加密 STUN/TURN
- 长期凭证认证
- 短期凭证用于 WebRTC
- 防止未授权使用 TURN 资源

## 故障排查

常见问题：
- **Symmetric NAT** - 需要 TURN
- **防火墙阻止 UDP** - 使用 TCP TURN
- **端口受限** - 配置防火墙规则
- **STUN 服务器不可达** - 检查网络连接

## 相关协议

- [SIP.md](SIP.md) - VoIP 信令
- [DTLS.md](DTLS.md) - WebRTC 加密
- [QUIC.md](QUIC.md) - 也使用类似的 NAT 穿越技术
