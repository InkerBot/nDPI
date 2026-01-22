# UBNTAC2 协议文档

## 协议概述

- **协议全称**: Ubiquiti AirControl 2 Protocol (Ubiquiti AirControl 2 协议)
- **协议 ID**: NDPI_PROTOCOL_UBNTAC2 (31)
- **协议类别**: 网络管理协议
- **传输层**: UDP (端口 10001)

## 简介

UBNTAC2 是 Ubiquiti Networks 开发的网络设备发现和管理协议，用于 AirControl 2 网络管理软件。该协议允许自动发现和管理 Ubiquiti 无线网络设备（如 AirMAX、UniFi 等）。nDPI 可以检测 UBNTAC2 流量并提取设备版本信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "UBNTAC2",
    "ubntac2": {
      "version": "v5.6.9"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| version | string | UBNTAC2 协议或设备固件版本 | "v5.6.9" |

## 实际示例

### 示例 1: 设备发现

```json
{
  "ndpi": {
    "protocol": "UBNTAC2",
    "ubntac2": {
      "version": "v5.6.9"
    }
  }
}
```

### 示例 2: AirMAX 设备

```json
{
  "ndpi": {
    "protocol": "UBNTAC2",
    "ubntac2": {
      "version": "v6.0.3"
    }
  }
}
```

### 示例 3: UniFi 接入点

```json
{
  "ndpi": {
    "protocol": "UBNTAC2",
    "ubntac2": {
      "version": "v4.3.20"
    }
  }
}
```

## 注意事项

### 1. Ubiquiti 产品线

Ubiquiti Networks 主要产品：
- **AirMAX**: 室外无线网桥和基站
- **UniFi**: 企业级 Wi-Fi 系统
- **EdgeMAX**: 路由器和交换机
- **AmpliFi**: 家用 Wi-Fi 系统

### 2. 端口使用

UBNTAC2 相关端口：
- **UDP 10001**: 设备发现
- **TCP 22**: SSH 管理
- **TCP 443**: HTTPS Web 界面
- **UDP 3478**: STUN

### 3. AirControl 2

AirControl 2 是 Ubiquiti 的网络管理软件：
- 设备发现和配置
- 网络拓扑可视化
- 性能监控
- 固件升级
- 批量配置管理

### 4. 设备发现机制

UBNTAC2 使用广播/多播进行设备发现：
- 发送发现包到局域网
- 设备响应包含设备信息
- 包括 MAC 地址、IP 地址、型号、版本等

### 5. 安全考虑

**潜在风险**：
- 设备发现可能泄露网络拓扑
- 未加密的管理流量
- 默认凭据风险

**安全建议**：
- 更改默认密码
- 限制管理网络访问
- 使用 VLAN 隔离管理流量
- 启用防火墙规则
- 定期更新固件

### 6. UniFi vs AirControl

| 特性 | UniFi Controller | AirControl 2 |
|------|------------------|---------------|
| 目标设备 | UniFi 产品 | AirMAX 产品 |
| 部署方式 | 云端或本地 | 本地 Windows |
| 管理界面 | Web | Windows 应用 |
| 自动化 | 支持 | 有限 |

### 7. 固件版本

版本号格式通常为：`vX.Y.Z`
- X: 主版本号
- Y: 次版本号
- Z: 修订号

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_UBNTAC2, 行 1639-1643)
- **协议检测**: `src/lib/protocols/ubntac2.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_UBNTAC2:
  ndpi_serialize_start_of_block(serializer, "ubntac2");
  ndpi_serialize_string_string(serializer, "version", flow->protos.ubntac2.version);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_ubntac2()` - UBNTAC2 协议检测器

## 参考资料

- [Ubiquiti Networks Official Website](https://www.ui.com/)
- [AirControl 2 Documentation](https://www.ui.com/download/aircontrol/)
- [UniFi Controller Documentation](https://help.ui.com/hc/en-us/categories/200320654-UniFi)
- [nDPI UBNTAC2 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/ubntac2.c)

## 常见用例

### 1. 无线网络部署

大规模 Wi-Fi 网络的部署和管理。

### 2. 室外无线链路

点对点或点对多点无线连接。

### 3. WISP (无线互联网服务提供商)

提供无线互联网接入服务。

### 4. 企业网络

企业级无线网络基础设施。

### 5. 网络监控

监控和优化无线网络性能。

### 6. 设备资产管理

自动发现和清点网络设备。

### 7. 固件管理

批量更新设备固件。

## Ubiquiti 设备管理

### 设备发现

```bash
# 使用 nmap 扫描 Ubiquiti 设备
nmap -p 10001 -sU --script ubiquiti-discovery 192.168.1.0/24
```

### SSH 访问

```bash
# 默认凭据（应立即更改）
ssh ubnt@device-ip
# 默认密码: ubnt
```

### Web 界面

```
https://device-ip
# 默认用户名: ubnt
# 默认密码: ubnt
```

## 网络架构

典型的 Ubiquiti 无线网络架构：

```
[Internet]
    |
[EdgeRouter]
    |
[UniFi Switch]
    |
+---+---+---+
|   |   |   |
AP  AP  AP  AP (UniFi Access Points)
```

## 性能监控

AirControl 2 提供的监控指标：
- 信号强度
- 链路质量
- 吞吐量
- 延迟
- 错误率
- 客户端数量

## 配置管理

### 批量配置

- 使用 AirControl 2 批量配置设备
- 配置模板
- 自动化脚本

### 备份和恢复

```bash
# 备份配置
scp ubnt@device-ip:/tmp/system.cfg ./backup.cfg

# 恢复配置
scp ./backup.cfg ubnt@device-ip:/tmp/system.cfg
# 通过 Web 界面恢复
```

## 故障排查

### 设备无法发现

- 检查网络连接
- 验证防火墙规则
- 确认设备在同一子网
- 检查 AirControl 2 设置

### 连接问题

- 验证设备固件版本
- 检查无线信号强度
- 确认信道设置
- 检查干扰源

### 性能问题

- 优化信道选择
- 调整传输功率
- 更新固件
- 检查网络拥塞

## 最佳实践

1. **网络规划**
   - 进行站点勘测
   - 优化 AP 放置
   - 选择合适的信道

2. **安全配置**
   - 更改默认密码
   - 启用 WPA3
   - 使用 VLAN 隔离
   - 定期安全审计

3. **性能优化**
   - 使用 5GHz 频段
   - 启用 airMAX 技术
   - 优化信道宽度
   - 调整 QoS 设置

4. **维护管理**
   - 定期更新固件
   - 监控设备健康状态
   - 备份配置
   - 文档化网络拓扑

## 相关协议

- [SNMP.md](SNMP.md) - 也用于网络设备管理
- [STUN.md](STUN.md) - Ubiquiti 设备使用 STUN
