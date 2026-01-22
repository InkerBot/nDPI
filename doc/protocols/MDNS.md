# MDNS 协议文档

## 协议概述

- **协议全称**: Multicast DNS (多播域名系统)
- **协议 ID**: NDPI_PROTOCOL_MDNS (8)
- **协议类别**: 网络服务发现
- **传输层**: UDP (端口 5353)

## 简介

mDNS 是一种在小型网络中实现零配置服务发现的协议，常用于局域网设备发现。它是 Zeroconf 技术栈的一部分，广泛应用于 Apple Bonjour、Avahi 等服务发现机制。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "MDNS",
    "hostname": "MyComputer.local",
    "mdns": {
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| hostname | string | 从 mDNS 查询/响应中提取的主机名 | "MyComputer.local" |

### 注意

当前 nDPI 实现中，mDNS 块主要用于协议识别，具体的服务信息提取有限。主机名信息会出现在外层的 `hostname` 字段中。

## 实际示例

### 示例 1: mDNS 查询

```json
{
  "ndpi": {
    "protocol": "MDNS",
    "hostname": "_services._dns-sd._udp.local",
    "mdns": {
    }
  }
}
```

### 示例 2: mDNS 设备公告

```json
{
  "ndpi": {
    "protocol": "MDNS",
    "hostname": "Living-Room-Speaker.local",
    "mdns": {
    }
  }
}
```

### 示例 3: Apple 设备 Bonjour

```json
{
  "ndpi": {
    "protocol": "MDNS",
    "hostname": "MacBook-Pro.local",
    "mdns": {
    }
  }
}
```

## mDNS 常见服务类型

| 服务类型 | 说明 |
|---------|------|
| _http._tcp.local | HTTP Web 服务器 |
| _ssh._tcp.local | SSH 服务器 |
| _smb._tcp.local | Samba/Windows 文件共享 |
| _airplay._tcp.local | Apple AirPlay |
| _googlecast._tcp.local | Google Cast/Chromecast |
| _spotify-connect._tcp.local | Spotify Connect |
| _ipp._tcp.local | 网络打印服务 |
| _afpovertcp._tcp.local | Apple 文件协议 |

## 注意事项

### 1. .local 域名

- mDNS 使用 `.local` 作为顶级域名
- 这些域名不会发送到常规 DNS 服务器
- 只在本地链路上解析

### 2. 多播地址

- IPv4: 224.0.0.251
- IPv6: FF02::FB
- 端口: 5353 (UDP)

### 3. 服务发现

mDNS 常与 DNS-SD (DNS Service Discovery) 结合使用：
- 发现网络中可用的服务
- 查询服务的详细信息（端口、参数等）

### 4. 安全考虑

- mDNS 流量可泄露设备信息
- 应限制 mDNS 流量跨越网络边界
- 某些网络环境禁用 mDNS 以提高安全性

### 5. 协议关系

- mDNS 是 DNS 的变体
- 使用相同的数据包格式
- nDPI 将其作为独立协议识别

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_MDNS, 行 1634-1637)
- **协议检测**: `src/lib/protocols/mdns.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_MDNS:
  ndpi_serialize_start_of_block(serializer, "mdns");
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_mdns()` - mDNS 协议检测器

## 参考资料

- [RFC 6762 - Multicast DNS](https://tools.ietf.org/html/rfc6762)
- [RFC 6763 - DNS-Based Service Discovery](https://tools.ietf.org/html/rfc6763)
- [Apple Bonjour](https://developer.apple.com/bonjour/)
- [Avahi - Zero Configuration Networking](https://www.avahi.org/)
- [nDPI mDNS 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/mdns.c)

## 常见用例

### 1. 局域网设备发现

自动发现网络中的打印机、智能设备等。

### 2. Apple 生态系统

- AirPlay 设备发现
- AirDrop 功能
- 共享服务发现

### 3. 智能家居

发现和连接智能家居设备（智能音箱、智能灯泡等）。

### 4. 网络管理

- 自动发现网络资产
- 监控设备上线/下线
- 网络拓扑映射

### 5. 流媒体

- Chromecast 设备发现
- DLNA 媒体服务器发现
- Spotify Connect 设备发现

### 6. 开发和调试

开发者工具使用 mDNS 发现本地开发服务器和服务。
