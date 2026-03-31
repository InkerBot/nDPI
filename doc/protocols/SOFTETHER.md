# SoftEther 协议文档

## 协议概述

- **协议全称**: SoftEther VPN Protocol (SoftEther VPN 协议)
- **协议 ID**: NDPI_PROTOCOL_SOFTETHER (290)
- **协议类别**: VPN 协议
- **传输层**: TCP/UDP (多种端口)

## 简介

SoftEther VPN 是一个开源的跨平台多协议 VPN 软件，由日本筑波大学开发。它支持 SSL-VPN、L2TP/IPsec、OpenVPN、MS-SSTP 等多种 VPN 协议。nDPI 可以检测 SoftEther 流量并提取客户端信息和服务器信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "SoftEther",
    "softether": {
      "client_ip": "192.168.1.100",
      "client_port": "54321",
      "hostname": "vpn-server",
      "fqdn": "vpn.example.com"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| client_ip | string | 客户端 IP 地址 | "192.168.1.100" |
| client_port | string | 客户端端口 | "54321" |
| hostname | string | 服务器主机名 | "vpn-server" |
| fqdn | string | 服务器完全限定域名 | "vpn.example.com" |

## 实际示例

### 示例 1: 标准 SoftEther VPN 连接

```json
{
  "ndpi": {
    "protocol": "SoftEther",
    "softether": {
      "client_ip": "10.0.1.50",
      "client_port": "52341",
      "hostname": "vpn01",
      "fqdn": "vpn01.company.net"
    }
  }
}
```

### 示例 2: 远程办公连接

```json
{
  "ndpi": {
    "protocol": "SoftEther",
    "softether": {
      "client_ip": "192.168.100.25",
      "client_port": "49876",
      "hostname": "remote-vpn",
      "fqdn": "remote.enterprise.org"
    }
  }
}
```

### 示例 3: 站点到站点 VPN

```json
{
  "ndpi": {
    "protocol": "SoftEther",
    "softether": {
      "client_ip": "172.16.1.1",
      "client_port": "55001",
      "hostname": "branch-gateway",
      "fqdn": "gateway.branch.company.com"
    }
  }
}
```

## 注意事项

### 1. SoftEther VPN 特点

**主要优势**：
- 开源免费
- 跨平台（Windows、Linux、macOS、FreeBSD）
- 支持多种 VPN 协议
- 高性能（比 OpenVPN 更快）
- 易于配置和管理
- 穿透 NAT 和防火墙能力强

**支持的协议**：
- SSL-VPN (HTTPS)
- L2TP/IPsec
- OpenVPN
- MS-SSTP
- EtherIP

### 2. 端口使用

SoftEther VPN 可使用多种端口：
- **TCP 443**: HTTPS/SSL-VPN（默认，易穿透防火墙）
- **TCP 992, 5555**: 其他常用端口
- **UDP 500, 4500**: L2TP/IPsec
- **UDP 1194**: OpenVPN
- 可自定义端口

### 3. VPN 工作模式

SoftEther 支持多种连接模式：
- **远程访问 VPN**: 客户端到站点
- **站点到站点 VPN**: 网络到网络
- **桥接模式**: 第二层桥接
- **SecureNAT**: 内置 NAT 和 DHCP

### 4. 安全特性

**加密和认证**：
- SSL 3.0/TLS 1.0 加密
- RSA 2048 位密钥
- AES 256 位加密
- 支持证书认证
- 支持 RADIUS 认证
- 支持 Active Directory 集成

**安全功能**：
- 动态 DNS 功能
- 虚拟 NAT 和 DHCP
- 访问控制列表
- 流量日志和监控

### 5. 与其他 VPN 的比较

| 特性 | SoftEther | OpenVPN | IPsec |
|------|-----------|---------|-------|
| 开源 | 是 | 是 | 部分 |
| 性能 | 高 | 中 | 高 |
| 易用性 | 易 | 中 | 难 |
| 穿透能力 | 强 | 强 | 弱 |
| 多协议 | 是 | 否 | 否 |

### 6. SoftEther VPN Server

SoftEther VPN Server 功能：
- 支持无限用户
- 虚拟 HUB 管理
- 本地桥接
- SecureNAT
- 级联连接
- VPN over ICMP/DNS

### 7. 客户端类型

- **SoftEther VPN Client**: 完整功能客户端
- **L2TP/IPsec 客户端**: Windows/macOS 内置
- **OpenVPN 客户端**: 标准 OpenVPN 客户端
- **MS-SSTP 客户端**: Windows 内置

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_SOFTETHER, 行 1653-1660)
- **协议检测**: `src/lib/protocols/softether.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_SOFTETHER:
  ndpi_serialize_start_of_block(serializer, "softether");
  ndpi_serialize_string_string(serializer, "client_ip", flow->protos.softether.ip);
  ndpi_serialize_string_string(serializer, "client_port", flow->protos.softether.port);
  ndpi_serialize_string_string(serializer, "hostname", flow->protos.softether.hostname);
  ndpi_serialize_string_string(serializer, "fqdn", flow->protos.softether.fqdn);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_softether()` - SoftEther 协议检测器

## 参考资料

- [SoftEther VPN Project](https://www.softether.org/)
- [SoftEther VPN Documentation](https://www.softether.org/4-docs)
- [SoftEther GitHub Repository](https://github.com/SoftEtherVPN/SoftEtherVPN)
- [nDPI SoftEther 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/softether.c)

## 常见用例

### 1. 远程办公

员工从家中或其他地点安全访问公司网络。

### 2. 站点互联

连接多个办公地点，构建统一网络。

### 3. 绕过审查

在网络审查严格的地区访问互联网（合法用途）。

### 4. 安全通信

保护公共 Wi-Fi 上的通信安全。

### 5. 云资源访问

安全访问云端资源和服务。

### 6. 开发和测试

开发人员访问测试环境和服务器。

### 7. IoT 设备管理

远程管理和监控 IoT 设备。

## 部署架构

### 简单远程访问

```
[客户端] --Internet--> [SoftEther VPN Server] ---> [内网资源]
```

### 站点到站点

```
[分支机构] <--VPN--> [总部] <--VPN--> [数据中心]
```

### 级联连接

```
[VPN Server 1] <--级联--> [VPN Server 2] <--级联--> [VPN Server 3]
```

## 配置示例

### 服务器配置要点

- 创建虚拟 HUB
- 配置用户认证
- 启用 SecureNAT 或本地桥接
- 设置访问规则
- 配置动态 DNS

### 客户端连接

```bash
# 使用 vpncmd 连接
vpncmd /client localhost /cmd AccountConnect MyVPN

# 或使用 GUI 客户端
```

## 性能考虑

- **吞吐量**: 可达 900+ Mbps（千兆网络）
- **延迟**: 通常 < 10ms（局域网）
- **并发连接**: 支持数千并发连接
- **加密开销**: 约 10-15% CPU 开销

## 安全最佳实践

1. **启用强认证**
   - 使用证书认证
   - 启用两因素认证
   - 定期更换密码

2. **网络隔离**
   - 使用访问控制列表
   - 限制访问范围
   - 分离 VPN 网段

3. **监控和审计**
   - 启用连接日志
   - 监控异常活动
   - 定期审查访问记录

4. **更新维护**
   - 保持软件最新
   - 及时应用安全补丁
   - 定期备份配置

5. **加密设置**
   - 使用最高加密级别
   - 禁用弱密码套件
   - 验证证书有效性

## 故障排查

### 常见问题

1. **无法连接**
   - 检查防火墙规则
   - 验证服务器运行状态
   - 确认端口正确

2. **连接频繁断开**
   - 检查网络稳定性
   - 调整 keepalive 设置
   - 验证 NAT 超时设置

3. **性能问题**
   - 选择更快的协议（SSL-VPN）
   - 调整加密级别
   - 优化网络路由

4. **认证失败**
   - 验证用户名密码
   - 检查账户状态
   - 确认认证服务器可达

## 与其他 VPN 的集成

SoftEther 可与标准 VPN 客户端互操作：
- Windows L2TP/IPsec
- macOS L2TP/IPsec
- iPhone/iPad L2TP/IPsec
- Android L2TP/IPsec
- OpenVPN 客户端

## 高可用性

实现 HA 的方法：
- 多服务器部署
- 负载均衡
- 故障转移配置
- 数据库集群

## 相关协议

- [TLS.md](TLS.md) - SoftEther 使用 TLS 加密
- [SSH.md](SSH.md) - 另一种安全隧道协议
