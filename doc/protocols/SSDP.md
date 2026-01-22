# SSDP 协议文档

## 协议概述

- **协议全称**: Simple Service Discovery Protocol (简单服务发现协议)
- **协议 ID**: NDPI_PROTOCOL_SSDP (12)
- **协议类别**: 服务发现协议
- **传输层**: UDP (端口 1900)

## 简介

SSDP 是 UPnP (Universal Plug and Play) 架构的一部分，用于在局域网中发现网络服务和设备。SSDP 使用 HTTP over UDP 进行通信，允许设备通告其服务并响应服务查询。广泛应用于智能家居设备、媒体服务器、打印机等。

## JSON 输出格式

### 完整结构

SSDP 支持众多字段，以下是完整的 JSON 结构：

```json
{
  "ndpi": {
    "protocol": "SSDP",
    "ssdp": {
      "METHOD": "NOTIFY",
      "CACHE-CONTROL": "max-age=1800",
      "LOCATION": "http://192.168.1.100:8080/description.xml",
      "NT": "urn:schemas-upnp-org:device:MediaServer:1",
      "NTS": "ssdp:alive",
      "SERVER": "Linux/5.4 UPnP/1.1 Device/1.0",
      "USN": "uuid:12345678-1234-1234-1234-123456789abc::urn:schemas-upnp-org:device:MediaServer:1",
      "X-RINCON-HOUSEHOLD": "Sonos_aabbccdd",
      "X-RINCON-BOOTSEQ": "1",
      "BOOTID.UPNP.ORG": "1",
      "X-RINCON-WIFIMODE": "0",
      "X-RINCON-VARIANT": "0",
      "HOUSEHOLD.SMARTSPEAKER.AUDIO": "Sonos",
      "LOCATION.SMARTSPEAKER.AUDIO": "http://192.168.1.100:1400",
      "SECURELOCATION.UPNP.ORG": "https://192.168.1.100:1443",
      "X-SONOS-HHSECURELOCATION": "https://192.168.1.100:1443",
      "MAN": "ssdp:discover",
      "MX": "3",
      "ST": "ssdp:all",
      "USER_AGENT": "UPnP/1.1 DLNADOC/1.50"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| METHOD | string | HTTP 方法 | "NOTIFY", "M-SEARCH" |
| CACHE-CONTROL | string | 缓存控制 | "max-age=1800" |
| LOCATION | string | 设备描述 XML 的 URL | "http://...xml" |
| NT | string | 通知类型 | "upnp:rootdevice" |
| NTS | string | 通知子类型 | "ssdp:alive", "ssdp:byebye" |
| SERVER | string | 服务器信息 | "Linux/5.4 UPnP/1.1" |
| USN | string | 唯一服务名称 | "uuid:..." |
| X-RINCON-HOUSEHOLD | string | Sonos 家庭 ID | "Sonos_xxx" |
| X-RINCON-BOOTSEQ | string | Sonos 启动序列 | "1" |
| BOOTID.UPNP.ORG | string | UPnP 启动 ID | "1" |
| X-RINCON-WIFIMODE | string | Sonos Wi-Fi 模式 | "0" |
| X-RINCON-VARIANT | string | Sonos 变体 | "0" |
| HOUSEHOLD.SMARTSPEAKER.AUDIO | string | 智能音箱家庭 | "Sonos" |
| LOCATION.SMARTSPEAKER.AUDIO | string | 智能音箱位置 | "http://..." |
| SECURELOCATION.UPNP.ORG | string | UPnP 安全位置 | "https://..." |
| X-SONOS-HHSECURELOCATION | string | Sonos 安全位置 | "https://..." |
| MAN | string | M-SEARCH 必需 | "ssdp:discover" |
| MX | string | 最大等待时间（秒） | "3" |
| ST | string | 搜索目标 | "ssdp:all" |
| USER_AGENT | string | 用户代理 | "UPnP/1.1" |

## 实际示例

### 示例 1: 设备通告 (NOTIFY)

```json
{
  "ndpi": {
    "protocol": "SSDP",
    "ssdp": {
      "METHOD": "NOTIFY",
      "CACHE-CONTROL": "max-age=1800",
      "LOCATION": "http://192.168.1.50:49152/description.xml",
      "NT": "upnp:rootdevice",
      "NTS": "ssdp:alive",
      "SERVER": "Linux/4.9 UPnP/1.0 MiniUPnPd/2.1",
      "USN": "uuid:device-uuid::upnp:rootdevice"
    }
  }
}
```

### 示例 2: 设备搜索 (M-SEARCH)

```json
{
  "ndpi": {
    "protocol": "SSDP",
    "ssdp": {
      "METHOD": "M-SEARCH",
      "MAN": "ssdp:discover",
      "MX": "5",
      "ST": "urn:schemas-upnp-org:device:MediaRenderer:1",
      "USER_AGENT": "VLC/3.0.11 UPnP/1.0"
    }
  }
}
```

### 示例 3: Sonos 音箱通告

```json
{
  "ndpi": {
    "protocol": "SSDP",
    "ssdp": {
      "METHOD": "NOTIFY",
      "CACHE-CONTROL": "max-age=1800",
      "LOCATION": "http://192.168.1.100:1400/xml/device_description.xml",
      "NT": "urn:schemas-upnp-org:device:ZonePlayer:1",
      "NTS": "ssdp:alive",
      "SERVER": "Linux UPnP/1.0 Sonos/70.3-88290",
      "USN": "uuid:RINCON_xxx::urn:schemas-upnp-org:device:ZonePlayer:1",
      "X-RINCON-HOUSEHOLD": "Sonos_aabbccddee",
      "X-RINCON-BOOTSEQ": "42",
      "X-RINCON-WIFIMODE": "0",
      "HOUSEHOLD.SMARTSPEAKER.AUDIO": "Sonos_aabbccddee"
    }
  }
}
```

### 示例 4: 设备离线通告

```json
{
  "ndpi": {
    "protocol": "SSDP",
    "ssdp": {
      "METHOD": "NOTIFY",
      "NT": "upnp:rootdevice",
      "NTS": "ssdp:byebye",
      "USN": "uuid:device-uuid::upnp:rootdevice"
    }
  }
}
```

## 注意事项

### 1. SSDP 消息类型

**NOTIFY**:
- 设备通告其存在
- 定期发送（缓存超时前刷新）
- `ssdp:alive`: 设备可用
- `ssdp:byebye`: 设备离线

**M-SEARCH**:
- 客户端搜索设备/服务
- 多播到 239.255.255.250:1900
- 设备以单播响应

**响应**:
- HTTP 200 OK 响应 M-SEARCH
- 包含设备描述信息

### 2. 多播地址

- **IPv4**: 239.255.255.250:1900
- **IPv6**: [FF02::C]:1900, [FF05::C]:1900

### 3. UPnP 设备类型

常见的 UPnP 设备类型 (NT/ST):
- `upnp:rootdevice`: 根设备
- `urn:schemas-upnp-org:device:MediaServer:*`: 媒体服务器
- `urn:schemas-upnp-org:device:MediaRenderer:*`: 媒体渲染器
- `urn:schemas-upnp-org:service:ContentDirectory:*`: 内容目录
- `urn:dial-multiscreen-org:service:dial:*`: DIAL (Discovery and Launch)

### 4. Sonos 特有字段

Sonos 音箱使用多个 X-RINCON-* 字段：
- **HOUSEHOLD**: 识别同一家庭的音箱
- **BOOTSEQ**: 重启计数器
- **WIFIMODE**: Wi-Fi 模式状态
- **VARIANT**: 音箱变体信息

### 5. 安全考虑

**SSDP 的安全问题**：
- 可用于 DDoS 放大攻击
- 泄露网络设备信息
- 无认证机制
- 易被欺骗

**安全建议**：
- 在防火墙阻止外部 SSDP
- 禁用不需要的 UPnP 服务
- 监控异常 SSDP 流量
- 使用设备隔离（IoT VLAN）

### 6. DLNA 和 UPnP

DLNA (Digital Living Network Alliance) 基于 UPnP：
- 媒体服务器和客户端互操作
- SSDP 用于设备发现
- 支持音频、视频、图片流

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_SSDP, 行 1830-1914)
- **协议检测**: `src/lib/protocols/ssdp.c`

## 相关函数

### nDPI 检测函数

- `ndpi_search_ssdp()` - SSDP 协议检测器

## 参考资料

- [UPnP Device Architecture v2.0](https://openconnectivity.org/developer/specifications/upnp-resources/upnp/)
- [SSDP Specification](https://tools.ietf.org/html/draft-cai-ssdp-v1-03)
- [DLNA Guidelines](https://www.dlna.org/)
- [nDPI SSDP 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/ssdp.c)

## 常见用例

### 1. 智能家居设备发现

智能音箱、智能灯泡、智能插座等设备发现。

### 2. 媒体流

DLNA 媒体服务器和播放器之间的通信。

### 3. 打印机发现

网络打印机自动发现。

### 4. 游戏主机

PlayStation、Xbox 的网络服务发现。

### 5. IoT 设备管理

物联网设备的自动配置和管理。

### 6. 网络监控

发现和监控网络中的 UPnP 设备。

### 7. 安全审计

检测未授权或异常的 UPnP 设备。

## SSDP 工具

### 发现设备

```bash
# 使用 gssdp-discover (Linux)
gssdp-discover

# 使用 Python
import ssdp
devices = ssdp.discover("ssdp:all")

# 使用 curl 发送 M-SEARCH
echo -ne "M-SEARCH * HTTP/1.1\r\nHOST: 239.255.255.250:1900\r\nMAN: \"ssdp:discover\"\r\nMX: 3\r\nST: ssdp:all\r\n\r\n" | nc -u 239.255.255.250 1900
```

### 监听 SSDP 流量

```bash
# tcpdump
tcpdump -i any -A port 1900

# Wireshark
# 过滤器: ssdp
```

## DDoS 放大攻击

SSDP 可被用于 DDoS 放大：
- 伪造源 IP 发送 M-SEARCH
- 多个设备响应到受害者
- 放大倍数可达 30-50 倍

防护措施：
- 在边界路由器过滤 SSDP
- 限速 SSDP 响应
- 禁用不必要的 UPnP

## UPnP 安全问题

历史安全漏洞：
- UPnPnP (2013): 数千万设备易受攻击
- CallStranger (2020): SSRF 漏洞
- 端口转发滥用

建议：
- 保持设备固件更新
- 禁用面向互联网的 UPnP
- 使用安全的 UPnP 实现

## 相关协议

- [HTTP.md](HTTP.md) - SSDP 使用 HTTP 格式
- [MDNS.md](MDNS.md) - 另一种服务发现协议
