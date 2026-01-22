# NTP 协议文档

## 协议概述

- **协议全称**: Network Time Protocol (网络时间协议)
- **协议 ID**: NDPI_PROTOCOL_NTP (9)
- **协议类别**: 网络服务
- **传输层**: UDP (端口 123)

## 简介

NTP 是用于在计算机系统之间同步时钟的网络协议。nDPI 可以检测 NTP 流量并提取协议版本和工作模式信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "NTP",
    "ntp": {
      "version": 4,
      "mode": 3
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| version | uint32 | NTP 协议版本号 | 3, 4 |
| mode | uint32 | NTP 工作模式 | 1-7 |

### NTP 版本

| 版本 | 说明 |
|------|------|
| 1 | NTPv1 (已过时) |
| 2 | NTPv2 (已过时) |
| 3 | NTPv3 (RFC 1305) |
| 4 | NTPv4 (RFC 5905, 当前标准) |

### NTP 模式

| 模式 | 名称 | 说明 |
|------|------|------|
| 0 | Reserved | 保留 |
| 1 | Symmetric Active | 对称主动模式 |
| 2 | Symmetric Passive | 对称被动模式 |
| 3 | Client | 客户端模式 |
| 4 | Server | 服务器模式 |
| 5 | Broadcast | 广播模式 |
| 6 | NTP Control Message | NTP 控制消息 |
| 7 | Reserved for Private Use | 保留私用 |

## 实际示例

### 示例 1: NTP 客户端请求

```json
{
  "ndpi": {
    "protocol": "NTP",
    "ntp": {
      "version": 4,
      "mode": 3
    }
  }
}
```

### 示例 2: NTP 服务器响应

```json
{
  "ndpi": {
    "protocol": "NTP",
    "ntp": {
      "version": 4,
      "mode": 4
    }
  }
}
```

### 示例 3: NTP 对称主动模式

```json
{
  "ndpi": {
    "protocol": "NTP",
    "ntp": {
      "version": 4,
      "mode": 1
    }
  }
}
```

### 示例 4: NTP 广播模式

```json
{
  "ndpi": {
    "protocol": "NTP",
    "ntp": {
      "version": 4,
      "mode": 5
    }
  }
}
```

## 注意事项

### 1. NTP 池服务器

- 客户端通常使用 mode = 3
- 服务器响应使用 mode = 4
- 公共 NTP 服务器：time.google.com, pool.ntp.org

### 2. SNTP (Simple NTP)

- SNTP 是 NTP 的简化版本
- 使用相同的数据包格式
- nDPI 将其识别为 NTP 协议

### 3. 时间同步层级

NTP 使用分层架构（Stratum）：
- Stratum 0: 原子钟、GPS 等参考时钟
- Stratum 1: 直接连接 Stratum 0 的服务器
- Stratum 2-15: 通过网络同步的服务器

### 4. 安全性

- NTP 可能被用于 DDoS 放大攻击
- 监控异常的 NTP 流量模式很重要
- 建议使用 NTP 认证机制

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_NTP, 行 1627-1632)
- **协议检测**: `src/lib/protocols/ntp.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_NTP:
  ndpi_serialize_start_of_block(serializer, "ntp");
  ndpi_serialize_string_uint32(serializer, "version", flow->protos.ntp.version);
  ndpi_serialize_string_uint32(serializer, "mode", flow->protos.ntp.mode);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_ntp()` - NTP 协议检测器

## 参考资料

- [RFC 5905 - Network Time Protocol Version 4](https://tools.ietf.org/html/rfc5905)
- [RFC 4330 - Simple Network Time Protocol (SNTP) Version 4](https://tools.ietf.org/html/rfc4330)
- [RFC 1305 - Network Time Protocol (Version 3)](https://tools.ietf.org/html/rfc1305)
- [NTP Pool Project](https://www.ntppool.org/)
- [nDPI NTP 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/ntp.c)

## 常见用例

### 1. 时间同步监控

监控网络设备的时间同步状态和质量。

### 2. 安全审计

检测未授权的 NTP 服务器访问。

### 3. DDoS 攻击检测

识别 NTP 放大攻击流量。

### 4. 网络性能分析

分析 NTP 流量模式，评估时间同步准确性。

### 5. 合规性检查

确保所有设备使用授权的 NTP 服务器。
