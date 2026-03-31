# COLLECTD 协议文档

## 协议概述

- **协议全称**: Collectd Network Protocol (Collectd 网络协议)
- **协议 ID**: NDPI_PROTOCOL_COLLECTD (298)
- **协议类别**: 系统监控协议
- **传输层**: UDP (默认端口 25826)

## 简介

Collectd 是一个系统统计数据收集守护进程，用于定期收集系统和应用程序性能指标。Collectd 网络协议允许 collectd 实例之间通过网络传输统计数据。nDPI 可以检测 collectd 流量并提取客户端用户名信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "Collectd",
    "collectd": {
      "client_username": "monitoring_user"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| client_username | string | Collectd 客户端用户名 | "monitoring_user" |

## 实际示例

### 示例 1: 标准 Collectd 连接

```json
{
  "ndpi": {
    "protocol": "Collectd",
    "collectd": {
      "client_username": "collectd_agent"
    }
  }
}
```

### 示例 2: 企业监控系统

```json
{
  "ndpi": {
    "protocol": "Collectd",
    "collectd": {
      "client_username": "monitoring_server_01"
    }
  }
}
```

### 示例 3: 云环境监控

```json
{
  "ndpi": {
    "protocol": "Collectd",
    "collectd": {
      "client_username": "cloud_metrics_collector"
    }
  }
}
```

## 注意事项

### 1. Collectd 架构

Collectd 采用模块化架构：
- **读取插件**: 收集系统指标（CPU、内存、磁盘等）
- **写入插件**: 输出数据到目标（网络、文件、数据库等）
- **网络插件**: 通过网络发送/接收数据

### 2. 端口使用

- **UDP 25826**: 默认 collectd 网络端口
- 可配置为使用其他端口
- 支持多播和单播传输

### 3. 数据格式

Collectd 网络协议使用二进制格式：
- 紧凑的数据包结构
- 支持多种数据类型
- 可选的加密和签名

### 4. 安全性

**Collectd 安全选项**：
- **认证**: 用户名/密码认证
- **加密**: AES-256 加密传输
- **签名**: HMAC-SHA-256 完整性验证

**安全建议**：
- 启用加密和认证
- 限制网络访问
- 使用防火墙规则
- 定期更新软件

### 5. 收集的指标类型

Collectd 可收集多种系统指标：
- **CPU 使用率**: 用户、系统、空闲时间
- **内存使用**: 可用、已用、缓存
- **磁盘 I/O**: 读写速率、IOPS
- **网络流量**: 接收/发送字节数
- **进程信息**: 进程数、线程数
- **应用指标**: 数据库、Web 服务器等

### 6. 部署模式

**客户端-服务器模式**：
- 多个 collectd 实例发送数据到中央服务器
- 服务器聚合和存储数据

**对等模式**：
- Collectd 实例之间互相传输数据
- 分布式监控架构

### 7. 字段可用性

- `client_username` 在启用认证时出现
- 未启用认证时可能为空
- 用于识别数据源

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_COLLECTD, 行 1591-1595)
- **协议检测**: `src/lib/protocols/collectd.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_COLLECTD:
  ndpi_serialize_start_of_block(serializer, "collectd");
  ndpi_serialize_string_string(serializer, "client_username", flow->protos.collectd.client_username);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_collectd()` - Collectd 协议检测器

## 参考资料

- [Collectd Official Website](https://collectd.org/)
- [Collectd Network Plugin Documentation](https://collectd.org/wiki/index.php/Plugin:Network)
- [Collectd Network Protocol Specification](https://collectd.org/wiki/index.php/Binary_protocol)
- [nDPI Collectd 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/collectd.c)

## 常见用例

### 1. 服务器监控

监控多台服务器的系统资源使用情况。

### 2. 性能分析

收集应用程序性能指标进行分析。

### 3. 容量规划

基于历史数据进行容量规划和预测。

### 4. 告警系统

当指标超过阈值时触发告警。

### 5. 分布式监控

构建大规模分布式监控系统。

### 6. 云监控

监控云基础设施和虚拟机。

### 7. DevOps

集成到 DevOps 工具链进行持续监控。

## Collectd 插件

### 常用读取插件

- **cpu**: CPU 使用率
- **memory**: 内存使用
- **disk**: 磁盘使用和 I/O
- **interface**: 网络接口统计
- **load**: 系统负载
- **processes**: 进程信息
- **sensors**: 硬件传感器（温度、风扇等）

### 常用写入插件

- **network**: 网络传输
- **rrdtool**: RRDtool 数据库
- **write_graphite**: Graphite 后端
- **write_http**: HTTP POST
- **csv**: CSV 文件
- **log**: 日志文件

## 监控栈集成

Collectd 常与以下工具配合使用：

- **Graphite**: 时间序列数据库
- **InfluxDB**: 时间序列数据库
- **Grafana**: 可视化仪表板
- **Prometheus**: 监控和告警系统
- **ELK Stack**: Elasticsearch, Logstash, Kibana

## 配置示例

### 发送端配置

```xml
<Plugin network>
  <Server "192.168.1.100" "25826">
    SecurityLevel Encrypt
    Username "collectd_client"
    Password "secure_password"
  </Server>
</Plugin>
```

### 接收端配置

```xml
<Plugin network>
  <Listen "0.0.0.0" "25826">
    SecurityLevel Encrypt
    AuthFile "/etc/collectd/passwd"
  </Listen>
</Plugin>
```

## 性能考虑

- **采样频率**: 默认 10 秒，可配置
- **数据包大小**: UDP MTU 限制（通常 1500 字节）
- **网络带宽**: 考虑监控数据量
- **CPU 开销**: 收集和传输的 CPU 使用

## 故障排查

### 常见问题

1. **数据未到达服务器**
   - 检查防火墙规则
   - 验证端口配置
   - 检查网络连接

2. **认证失败**
   - 验证用户名密码
   - 检查 SecurityLevel 设置
   - 确认 AuthFile 配置

3. **数据丢失**
   - UDP 不保证可靠传输
   - 考虑网络拥塞
   - 调整缓冲区大小

4. **性能问题**
   - 减少采样频率
   - 禁用不需要的插件
   - 优化网络传输

## 安全最佳实践

1. **启用加密**: 使用 AES-256 加密
2. **启用认证**: 设置强密码
3. **网络隔离**: 使用管理网络
4. **访问控制**: 限制源 IP
5. **定期审计**: 检查配置和日志

## 替代方案

现代监控解决方案：
- **Telegraf**: InfluxData 的监控代理
- **Prometheus Node Exporter**: Prometheus 生态
- **Datadog Agent**: SaaS 监控服务
- **Zabbix Agent**: Zabbix 监控系统
