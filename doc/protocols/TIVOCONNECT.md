# TiVoConnect 协议文档

## 协议概述

- **协议全称**: TiVoConnect Discovery Protocol (TiVoConnect 发现协议)
- **协议 ID**: NDPI_PROTOCOL_TIVOCONNECT (308)
- **协议类别**: 服务发现协议
- **传输层**: UDP (端口 2190)

## 简介

TiVoConnect 是 TiVo 数字录像机 (DVR) 使用的设备发现和服务通告协议。该协议允许 TiVo 设备在网络中自动发现彼此，以及被其他设备（如 TiVo Desktop、移动应用等）发现。nDPI 可以检测 TiVoConnect 流量并提取设备信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "TiVoConnect",
    "tivoconnect": {
      "identity_uuid": "12345678-90AB-CDEF-1234-567890ABCDEF",
      "machine": "TiVo_Premiere",
      "platform": "tcd/Series4",
      "services": "TiVoMediaServer:80/http"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| identity_uuid | string | 设备唯一标识符 (UUID) | "12345678-90AB-CDEF-1234-567890ABCDEF" |
| machine | string | 设备型号/机器名称 | "TiVo_Premiere" |
| platform | string | 平台标识符 | "tcd/Series4" |
| services | string | 提供的服务列表 | "TiVoMediaServer:80/http" |

## 实际示例

### 示例 1: TiVo Premiere DVR

```json
{
  "ndpi": {
    "protocol": "TiVoConnect",
    "tivoconnect": {
      "identity_uuid": "A1B2C3D4-E5F6-7890-ABCD-EF1234567890",
      "machine": "TiVo_Premiere",
      "platform": "tcd/Series4",
      "services": "TiVoMediaServer:80/http"
    }
  }
}
```

### 示例 2: TiVo BOLT 设备

```json
{
  "ndpi": {
    "protocol": "TiVoConnect",
    "tivoconnect": {
      "identity_uuid": "FEDCBA98-7654-3210-FEDC-BA9876543210",
      "machine": "TiVo_BOLT",
      "platform": "tcd/Series5",
      "services": "TiVoMediaServer:80/http,TiVoRemote:31339/tcp"
    }
  }
}
```

### 示例 3: TiVo Mini 客户端

```json
{
  "ndpi": {
    "protocol": "TiVoConnect",
    "tivoconnect": {
      "identity_uuid": "11223344-5566-7788-99AA-BBCCDDEEFF00",
      "machine": "TiVo_Mini",
      "platform": "tcd/MiniClient",
      "services": "TiVoMediaClient:80/http"
    }
  }
}
```

## 注意事项

### 1. TiVo 设备系列

TiVo 主要产品线：
- **Series 1-3**: 早期 TiVo DVR
- **Series 4**: Premiere, Premiere XL
- **Series 5**: Roamio, BOLT
- **Series 6**: EDGE, BOLT VOX
- **TiVo Mini**: 无硬盘客户端设备
- **TiVo Stream**: 流媒体设备

### 2. 端口使用

TiVoConnect 相关端口：
- **UDP 2190**: Beacon 广播
- **TCP 80**: TiVo Media Server (HTTP)
- **TCP 443**: HTTPS 服务
- **TCP 31339**: Remote Protocol
- **UDP 2191**: 另一个服务发现端口

### 3. TiVoConnect Discovery

发现机制：
- TiVo 设备定期发送 beacon 包
- Beacon 包含设备标识和服务信息
- 客户端监听 beacon 以发现设备
- 支持设备间的流媒体传输

### 4. 平台标识符

常见的 platform 值：
- `tcd/Series3`: Series 3 设备
- `tcd/Series4`: Premiere 系列
- `tcd/Series5`: Roamio/BOLT 系列
- `tcd/Series6`: EDGE/BOLT VOX
- `tcd/MiniClient`: TiVo Mini 客户端

### 5. 服务类型

TiVo 设备提供的服务：
- **TiVoMediaServer**: 媒体内容服务器
- **TiVoRemote**: 远程控制接口
- **TiVoVideoStream**: 视频流服务
- **TiVoBeacon**: 发现服务

### 6. UUID 格式

- 标准 UUID 格式（8-4-4-4-12）
- 用于唯一标识每个 TiVo 设备
- 不会随设备重启改变

### 7. TiVo 网络功能

**多房间查看 (MRV)**：
- 在不同 TiVo 设备间流式传输录制内容
- 通过 TiVoConnect 发现设备

**TiVo Desktop/Mobile**：
- PC 或移动设备访问 TiVo 内容
- 使用 TiVoConnect 发现 DVR

**TiVo Stream**：
- 将直播电视流式传输到移动设备
- 依赖 TiVoConnect 进行设备发现

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_TIVOCONNECT, 行 1701-1708)
- **协议检测**: `src/lib/protocols/tivoconnect.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_TIVOCONNECT:
  ndpi_serialize_start_of_block(serializer, "tivoconnect");
  ndpi_serialize_string_string(serializer, "identity_uuid", flow->protos.tivoconnect.identity_uuid);
  ndpi_serialize_string_string(serializer, "machine", flow->protos.tivoconnect.machine);
  ndpi_serialize_string_string(serializer, "platform", flow->protos.tivoconnect.platform);
  ndpi_serialize_string_string(serializer, "services", flow->protos.tivoconnect.services);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_tivoconnect()` - TiVoConnect 协议检测器

## 参考资料

- [TiVo Official Website](https://www.tivo.com/)
- [TiVo Community Forum](https://www.tivocommunity.com/)
- [TiVoConnect Reverse Engineering Info](https://www.tivo.com/assets/images/abouttivo/resources/downloads/brochures/TiVo_TCP_Network_Remote_Control_Protocol.pdf)
- [nDPI TiVoConnect 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/tivoconnect.c)

## 常见用例

### 1. 多房间查看

在家中多个 TiVo 设备间传输录制内容。

### 2. 移动访问

使用 TiVo 移动应用访问录制和直播内容。

### 3. PC 集成

使用 TiVo Desktop 管理和传输录制内容。

### 4. 设备管理

自动发现和配置 TiVo 网络。

### 5. 网络监控

监控 TiVo 设备在网络中的活动。

### 6. 故障排查

诊断 TiVo 设备连接和通信问题。

### 7. 资产清点

识别网络中的所有 TiVo 设备。

## TiVo 网络架构

典型的 TiVo 家庭网络：

```
[Internet/Cable]
      |
[Cable Card/Tuner]
      |
[TiVo DVR (主设备)]
      |
  [家庭网络]
      |
   +--+--+
   |     |
[TiVo Mini] [TiVo Mini]
(客户端)    (客户端)
```

## 发现 TiVo 设备

### 使用网络扫描

```bash
# nmap 扫描 TiVo 设备
nmap -p 2190,80,31339 -sU -sT 192.168.1.0/24

# tcpdump 监听 TiVoConnect beacon
tcpdump -i any -n port 2190
```

### 使用 Python

```python
import socket

# 监听 TiVoConnect beacon
sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
sock.bind(('', 2190))

while True:
    data, addr = sock.recvfrom(1024)
    print(f"TiVo device found at {addr}: {data}")
```

## TiVo Remote Protocol

TiVo 远程控制协议：
- 通过 TCP 端口 31339
- 发送键盘命令（如 TELEPORT, LIVETV）
- 用于自动化和第三方控制

示例命令：
```
IRCODE UP\r
IRCODE DOWN\r
IRCODE SELECT\r
TELEPORT LIVETV\r
```

## TiVo Desktop

TiVo Desktop 功能：
- 在 PC 上播放录制内容
- 将视频传输到 TiVo
- 管理 Season Pass
- 远程录制调度

## 流媒体质量

TiVo 流媒体参数：
- **Best**: 约 8-12 Mbps
- **High**: 约 4-6 Mbps  
- **Medium**: 约 2-3 Mbps
- **Low**: 约 1 Mbps

## 网络要求

TiVo 多房间功能的网络要求：
- 100 Mbps 以上的有线以太网（推荐）
- 802.11n/ac Wi-Fi（5GHz 推荐）
- 低延迟和稳定的连接
- 足够的带宽用于流媒体

## 故障排查

### 设备未被发现

- 检查设备是否在同一子网
- 验证防火墙规则
- 确认 TiVo 服务已启动
- 重启 TiVo 设备

### 流媒体质量问题

- 检查网络带宽
- 优先使用有线连接
- 减少网络拥塞
- 降低流质量设置

### 连接中断

- 检查网络稳定性
- 验证路由器设置
- 更新 TiVo 软件
- 检查 DHCP 租期

## 隐私和安全

TiVo 数据收集：
- 观看习惯
- 搜索查询
- 设备使用模式

隐私设置：
- 可在设置中禁用某些数据收集
- 检查隐私政策
- 管理广告跟踪

## TiVo vs 其他 DVR

| 特性 | TiVo | Cable Co DVR | Plex DVR |
|------|------|--------------|----------|
| 界面 | 优秀 | 一般 | 好 |
| 功能 | 丰富 | 基础 | 丰富 |
| 成本 | 高 | 包含 | 中等 |
| 跳过广告 | 有 | 有限 | 有 |

## 最佳实践

1. **网络配置**
   - 使用有线连接主 DVR
   - 5GHz Wi-Fi 用于 Mini
   - 配置 QoS 优先级

2. **性能优化**
   - 定期重启设备
   - 清理已观看内容
   - 管理 Season Pass

3. **安全设置**
   - 启用家长控制
   - 设置 PIN 码
   - 定期检查网络

4. **维护**
   - 保持软件更新
   - 监控硬盘健康
   - 备份设置

## 相关协议

- [SSDP.md](SSDP.md) - 另一种 UPnP 发现协议
- [MDNS.md](MDNS.md) - mDNS 服务发现
- [HTTP.md](HTTP.md) - TiVo 使用 HTTP 传输媒体
