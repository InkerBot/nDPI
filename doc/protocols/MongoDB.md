# MongoDB 协议文档

## 协议概述

- **协议全称**: MongoDB Wire Protocol (MongoDB 线协议)
- **协议 ID**: NDPI_PROTOCOL_MONGODB (60)
- **协议类别**: 数据库协议
- **传输层**: TCP (默认端口 27017)

## 简介

MongoDB 是一个流行的 NoSQL 文档数据库，使用自定义的二进制协议进行客户端和服务器通信。nDPI 可以识别 MongoDB 流量，但在当前版本中，JSON 输出不包含特定的协议字段。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "MongoDB"
  }
}
```

### 字段说明

当前 nDPI 实现中，MongoDB 协议检测主要用于识别流量类型，未提取具体的数据库操作信息。

## 实际示例

### 示例 1: MongoDB 连接

```json
{
  "ndpi": {
    "protocol": "MongoDB",
    "hostname": "mongodb.example.com"
  }
}
```

### 示例 2: 本地 MongoDB 实例

```json
{
  "ndpi": {
    "protocol": "MongoDB"
  }
}
```

## 注意事项

### 1. MongoDB 端口

| 端口 | 用途 |
|------|------|
| 27017 | MongoDB 实例默认端口 |
| 27018 | MongoDB Shard 服务器 |
| 27019 | MongoDB Config 服务器 |
| 28017 | MongoDB Web 状态页面（已弃用） |

### 2. MongoDB 架构

MongoDB 部署架构：
- **单机实例** - 独立 MongoDB 服务器
- **副本集 (Replica Set)** - 数据冗余和高可用
- **分片集群 (Sharded Cluster)** - 水平扩展

### 3. 连接字符串

MongoDB 连接字符串格式：
```
mongodb://[username:password@]host[:port][/database][?options]
mongodb+srv://[username:password@]host[/database][?options]
```

### 4. Wire Protocol 版本

MongoDB Wire Protocol 经历了多个版本：
- **OP_QUERY** / **OP_REPLY** - 传统操作码（已弃用）
- **OP_MSG** - 现代统一消息格式（MongoDB 3.6+）
- **OP_COMPRESSED** - 压缩消息支持

### 5. 安全性

**MongoDB 安全最佳实践**：
- 启用认证（默认需要启用）
- 使用 TLS/SSL 加密传输
- 限制网络访问（绑定到特定 IP）
- 使用防火墙规则
- 启用审计日志
- 定期更新和打补丁

**历史安全问题**：
- 早期版本默认不需要认证
- 暴露在互联网上的实例被攻击
- 勒索软件攻击未加密的 MongoDB

### 6. 流量特征

MongoDB 流量特点：
- 二进制协议（BSON 格式）
- 通常大量的小请求
- 可能有大的查询结果
- 持久连接

### 7. 性能监控

监控 MongoDB 的关键指标：
- 连接数
- 查询延迟
- 索引使用率
- 复制延迟
- 磁盘 I/O

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (MongoDB 未在 ndpi_dpi2json 中特殊处理)
- **协议检测**: `src/lib/protocols/mongodb.c`

## 相关函数

### nDPI 检测函数

- `ndpi_search_mongodb()` - MongoDB 协议检测器

## 参考资料

- [MongoDB Wire Protocol](https://www.mongodb.com/docs/manual/reference/mongodb-wire-protocol/)
- [MongoDB Official Documentation](https://www.mongodb.com/docs/)
- [MongoDB Security Checklist](https://www.mongodb.com/docs/manual/administration/security-checklist/)
- [nDPI MongoDB 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/mongodb.c)

## 常见用例

### 1. Web 应用数据库

现代 Web 应用使用 MongoDB 存储数据。

### 2. 内容管理系统

CMS 使用 MongoDB 存储文档和元数据。

### 3. 实时分析

MongoDB 用于实时数据分析和聚合。

### 4. 移动应用后端

移动 App 后端使用 MongoDB。

### 5. IoT 数据存储

物联网设备数据存储。

### 6. 缓存层

作为应用的缓存层使用。

### 7. 日志存储

应用日志和事件存储。

## MongoDB 操作类型

MongoDB 支持的主要操作：
- **CRUD 操作** - Create, Read, Update, Delete
- **聚合管道** - 复杂的数据处理
- **索引操作** - 创建和管理索引
- **事务** - 多文档 ACID 事务
- **变更流** - 实时数据变更通知

## 网络流量分析

监控 MongoDB 流量的目标：
- 识别异常的数据库访问模式
- 检测未加密的连接
- 监控外部访问尝试
- 分析查询性能
- 检测数据泄露

## 安全建议

1. **永远不要暴露到公网**
   - 使用 VPN 或跳板机访问
   - 绑定到内网 IP

2. **启用认证和授权**
   - 创建管理员账户
   - 使用基于角色的访问控制
   - 最小权限原则

3. **加密传输**
   - 使用 TLS/SSL
   - 配置证书验证

4. **加密存储**
   - 启用静态数据加密
   - 保护备份文件

5. **网络隔离**
   - 使用防火墙
   - VPC/VLAN 隔离
   - 安全组配置

6. **监控和审计**
   - 启用审计日志
   - 监控异常访问
   - 定期安全审查

## MongoDB Atlas

MongoDB Atlas 是官方的云数据库服务：
- 托管的 MongoDB 服务
- 自动备份和恢复
- 内置安全特性
- 全球分布式部署
- 自动扩展

## MongoDB Drivers

各语言的 MongoDB 驱动：
- **Python** - PyMongo
- **Node.js** - mongodb driver
- **Java** - MongoDB Java Driver
- **C#** - MongoDB.Driver
- **Go** - mongo-go-driver
- **Ruby** - mongo-ruby-driver
- **PHP** - mongodb extension

## 性能优化

MongoDB 性能优化建议：
- 创建合适的索引
- 使用投影减少返回数据
- 限制结果集大小
- 使用连接池
- 配置适当的读写关注级别
- 监控慢查询
- 使用聚合管道优化

## 故障排查

常见 MongoDB 问题：
- 连接被拒绝 - 检查防火墙和绑定 IP
- 认证失败 - 验证用户名密码和权限
- 性能问题 - 检查索引和慢查询
- 复制延迟 - 检查网络和服务器负载
- 磁盘空间不足 - 清理或扩展存储
