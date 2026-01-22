# MikroTik 协议文档

## 协议概述

- **协议全称**: MikroTik Neighbor Discovery Protocol (MikroTik 邻居发现协议)
- **协议 ID**: NDPI_PROTOCOL_MIKROTIK (437)
- **协议类别**: 网络管理和发现协议
- **传输层**: UDP (端口 5678)

## 简介

MikroTik Neighbor Discovery Protocol 是 MikroTik RouterOS 设备使用的自动发现协议，类似于 Cisco CDP 或 LLDP。该协议允许 MikroTik 路由器和交换机自动发现网络中的其他 MikroTik 设备，并交换设备信息。nDPI 可以检测该协议并提取丰富的设备信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "MikroTik",
    "mikrotik": {
      "mac_address": "AA:BB:CC:DD:EE:FF",
      "identity": "RouterOS-Main",
      "version": "6.48.3",
      "software_id": "ABCD-1234",
      "board": "RB4011iGS+",
      "iface_name": "ether1",
      "ipv4_addr": "192.168.88.1",
      "ipv6_addr": "fe80::1",
      "uptime": 864000
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| mac_address | string | 设备 MAC 地址 | "AA:BB:CC:DD:EE:FF" |
| identity | string | 设备标识名称 | "RouterOS-Main" |
| version | string | RouterOS 版本 | "6.48.3" |
| software_id | string | 软件许可证 ID | "ABCD-1234" |
| board | string | 硬件型号 | "RB4011iGS+" |
| iface_name | string | 接口名称 | "ether1" |
| ipv4_addr | string | IPv4 地址 | "192.168.88.1" |
| ipv6_addr | string | IPv6 地址 | "fe80::1" |
| uptime | uint32 | 设备运行时间（秒） | 864000 |

## 实际示例

### 示例 1: 企业核心路由器

```json
{
  "ndpi": {
    "protocol": "MikroTik",
    "mikrotik": {
      "mac_address": "4A:A9:8C:12:34:56",
      "identity": "Core-Router-01",
      "version": "7.6",
      "board": "CCR1036-12G-4S",
      "iface_name": "ether1",
      "ipv4_addr": "10.0.0.1",
      "uptime": 5184000
    }
  }
}
```

### 示例 2: 接入层交换机

```json
{
  "ndpi": {
    "protocol": "MikroTik",
    "mikrotik": {
      "mac_address": "E4:8D:8C:AB:CD:EF",
      "identity": "Switch-Floor2",
      "version": "6.48.6",
      "software_id": "WXYZ-5678",
      "board": "CRS328-24P-4S+",
      "iface_name": "sfp-sfpplus1",
      "ipv4_addr": "192.168.1.10",
      "uptime": 2592000
    }
  }
}
```

### 示例 3: 无线接入点

```json
{
  "ndpi": {
    "protocol": "MikroTik",
    "mikrotik": {
      "mac_address": "74:4D:28:11:22:33",
      "identity": "AP-Office",
      "version": "6.49.6",
      "board": "cAP ac",
      "iface_name": "wlan1",
      "ipv4_addr": "192.168.88.253",
      "ipv6_addr": "fe80::764d:28ff:fe11:2233",
      "uptime": 432000
    }
  }
}
```

## 注意事项

### 1. MikroTik RouterOS

RouterOS 是 MikroTik 设备的操作系统：
- 基于 Linux 内核
- 功能丰富的路由器操作系统
- 支持路由、防火墙、VPN、无线等
- 可通过 Winbox、WebFig、SSH、API 管理

### 2. 端口使用

MikroTik 相关端口：
- **UDP 5678**: Neighbor Discovery
- **TCP 8291**: Winbox
- **TCP 80/443**: WebFig (HTTP/HTTPS)
- **TCP 22**: SSH
- **TCP 8728**: API
- **TCP 8729**: API-SSL

### 3. Neighbor Discovery 工作原理

- 定期发送多播包到本地网络
- 包含设备标识和配置信息
- 邻居设备接收并显示信息
- 可在 Winbox Neighbors 中查看

### 4. 设备型号

常见 MikroTik 产品线：
- **CCR (Cloud Core Router)**: 高性能核心路由器
- **CRS (Cloud Router Switch)**: 三层交换机
- **RB (RouterBOARD)**: 各种路由器型号
- **hAP**: 家用/小型办公 Wi-Fi
- **cAP**: 企业级接入点
- **wAP**: 室外无线设备

### 5. RouterOS 版本

版本号格式：`X.Y.Z`
- **v6.x**: 稳定长期支持版本
- **v7.x**: 新一代版本（2021+）
- 建议使用稳定版本

### 6. 许可证级别

RouterOS 许可证级别：
- **Level 1**: 仅限 WISP 客户端
- **Level 3**: 家用
- **Level 4**: 小型办公（ISP）
- **Level 5**: 中型（ISP）
- **Level 6**: 无限制（企业）

### 7. 安全考虑

**Neighbor Discovery 风险**：
- 泄露网络拓扑信息
- 暴露设备型号和版本
- 可能被用于侦察

**安全建议**：
- 在不需要时禁用 Neighbor Discovery
- 使用防火墙限制访问
- 隔离管理网络
- 保持固件更新
- 更改默认凭据

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_MIKROTIK, 行 1784-1828)
- **协议检测**: `src/lib/protocols/mikrotik.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_MIKROTIK:
  {
    char buf[32];
    ndpi_serialize_start_of_block(serializer, "mikrotik");
    
    // MAC 地址
    snprintf(buf, sizeof(buf), "%02X:%02X:%02X:%02X:%02X:%02X",
             flow->protos.mikrotik.mac_addr[0] & 0xFF,
             flow->protos.mikrotik.mac_addr[1] & 0xFF,
             flow->protos.mikrotik.mac_addr[2] & 0xFF,
             flow->protos.mikrotik.mac_addr[3] & 0xFF,
             flow->protos.mikrotik.mac_addr[4] & 0xFF,
             flow->protos.mikrotik.mac_addr[5] & 0xFF);
    ndpi_serialize_string_string(serializer, "mac_address", buf);
    
    // 其他可选字段
    if(flow->protos.mikrotik.identity[0] != '\0')
      ndpi_serialize_string_string(serializer, "identity", flow->protos.mikrotik.identity);
    
    // ... 更多字段
    ndpi_serialize_end_of_block(serializer);
  }
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_mikrotik()` - MikroTik 协议检测器

## 参考资料

- [MikroTik Official Website](https://mikrotik.com/)
- [RouterOS Documentation](https://wiki.mikrotik.com/wiki/Manual:TOC)
- [Neighbor Discovery Protocol](https://wiki.mikrotik.com/wiki/Manual:IP/Neighbor_discovery)
- [nDPI MikroTik 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/mikrotik.c)

## 常见用例

### 1. 网络拓扑发现

自动发现和映射 MikroTik 网络拓扑。

### 2. 设备管理

快速识别和访问网络中的 MikroTik 设备。

### 3. 网络监控

监控设备状态、版本和运行时间。

### 4. 故障排查

快速定位网络中的设备和连接问题。

### 5. 资产清单

自动化设备资产管理和清点。

### 6. 固件管理

识别需要更新的设备。

### 7. 网络规划

了解现有网络架构，规划扩展。

## MikroTik 设备管理

### Winbox 连接

Winbox 可显示邻居设备：
- 打开 Winbox
- 点击 "Neighbors" 标签
- 查看所有发现的设备
- 点击设备可直接连接

### CLI 命令

```bash
# 查看邻居
/ip neighbor print

# 查看详细信息
/ip neighbor print detail

# 禁用 Neighbor Discovery
/ip neighbor discovery-settings set discover-interface-list=none
```

### API 访问

```python
# 使用 RouterOS API
import routeros_api

connection = routeros_api.RouterOsApiPool(
    '192.168.88.1',
    username='admin',
    password='password'
)

api = connection.get_api()
neighbors = api.get_resource('/ip/neighbor')
print(neighbors.get())
```

## 网络架构示例

典型的 MikroTik 网络：

```
          [Internet]
               |
      [CCR - Core Router]
               |
    +----------+----------+
    |          |          |
[CRS Switch] [CRS Switch] [CRS Switch]
    |          |          |
  [hAP]      [hAP]      [hAP]
  (Wi-Fi)    (Wi-Fi)    (Wi-Fi)
```

## 性能监控

监控 MikroTik 设备性能：
- **CPU 使用率**: `/system resource print`
- **内存使用**: `/system resource print`
- **接口流量**: `/interface monitor-traffic`
- **连接数**: `/ip firewall connection print count-only`
- **运行时间**: 从 uptime 字段获取

## 配置备份

```bash
# 导出配置
/export file=backup

# 备份系统
/system backup save name=backup

# 通过 SCP 下载备份
scp admin@192.168.88.1:backup.backup ./
```

## 常用 RouterOS 功能

- **路由**: BGP, OSPF, RIP, 静态路由
- **防火墙**: 包过滤、NAT、Mangle
- **QoS**: 队列树、Simple Queues
- **VPN**: PPTP, L2TP, OpenVPN, IPsec, WireGuard
- **无线**: 2.4GHz, 5GHz, Nstreme, Nv2
- **VLAN**: 802.1Q 标记
- **DHCP**: 服务器和客户端
- **DNS**: 缓存和静态记录

## 故障排查

### Neighbor Discovery 不工作

- 检查接口是否启用发现
- 验证多播未被阻止
- 确认设备在同一网络
- 检查 IP Neighbor Settings

### 连接问题

- 验证 IP 地址配置
- 检查防火墙规则
- 测试网络连接性
- 查看日志

### 性能问题

- 监控 CPU 使用率
- 检查内存使用
- 优化规则集
- 升级硬件

## 最佳实践

1. **网络设计**
   - 使用 VLAN 分隔流量
   - 实施冗余链路
   - 规划 IP 地址方案

2. **安全配置**
   - 更改默认密码
   - 禁用不需要的服务
   - 配置防火墙规则
   - 启用 MAC 服务器（谨慎）
   - 定期备份配置

3. **性能优化**
   - 使用硬件卸载
   - 优化防火墙规则顺序
   - 启用 FastTrack
   - 使用 Connection Tracking

4. **监控和维护**
   - 定期检查日志
   - 监控资源使用
   - 保持固件更新
   - 文档化配置

## RouterOS vs 其他网络操作系统

| 特性 | RouterOS | Cisco IOS | Junos |
|------|----------|-----------|-------|
| 价格 | 低廉 | 昂贵 | 昂贵 |
| 易用性 | 中等 | 难 | 难 |
| 功能 | 丰富 | 非常丰富 | 非常丰富 |
| 社区 | 活跃 | 大型 | 中等 |

## 相关协议

- [SNMP.md](SNMP.md) - MikroTik 也支持 SNMP 管理
- [SSH.md](SSH.md) - SSH 管理访问
- [DHCP.md](DHCP.md) - DHCP 服务器功能
