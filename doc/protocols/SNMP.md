# SNMP 协议文档

## 协议概述

- **协议全称**: Simple Network Management Protocol (简单网络管理协议)
- **协议 ID**: NDPI_PROTOCOL_SNMP (14)
- **协议类别**: 网络管理
- **传输层**: UDP (端口 161, 162)

## 简介

SNMP 是用于网络管理和监控的协议，允许管理员收集设备信息、配置网络设备并监控网络性能。nDPI 可以提取 SNMP 消息的版本、操作类型和错误状态。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "SNMP",
    "snmp": {
      "version": 2,
      "primitive": 0,
      "error_status": 0
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| version | uint32 | SNMP 协议版本 | 0, 1, 2, 3 |
| primitive | uint32 | SNMP PDU 类型（操作类型） | 0-8 |
| error_status | uint32 | 错误状态码 | 0-18 |

### SNMP 版本

| 版本号 | 版本名称 | 说明 |
|-------|---------|------|
| 0 | SNMPv1 | 原始版本，使用社区字符串 |
| 1 | SNMPv2c | 改进版本，兼容 v1 |
| 2 | SNMPv2u | 基于用户的安全模型（已弃用） |
| 3 | SNMPv3 | 增强安全性，支持加密和认证 |

### SNMP PDU 类型 (primitive)

| 值 | 名称 | 说明 |
|----|------|------|
| 0 | GetRequest | 获取一个或多个变量值 |
| 1 | GetNextRequest | 获取下一个变量值 |
| 2 | GetResponse | 响应 Get 请求 |
| 3 | SetRequest | 设置一个或多个变量值 |
| 4 | Trap | 异步通知（v1） |
| 5 | GetBulkRequest | 批量获取（v2+） |
| 6 | InformRequest | 确认通知（v2+） |
| 7 | SNMPv2-Trap | 陷阱通知（v2+） |
| 8 | Report | 报告（v3） |

### SNMP 错误状态 (error_status)

| 值 | 名称 | 说明 |
|----|------|------|
| 0 | noError | 无错误 |
| 1 | tooBig | 响应消息过大 |
| 2 | noSuchName | 变量不存在 |
| 3 | badValue | 值无效 |
| 4 | readOnly | 只读变量 |
| 5 | genErr | 一般错误 |
| 6 | noAccess | 访问被拒绝 |
| 7 | wrongType | 类型错误 |
| 8 | wrongLength | 长度错误 |
| 9 | wrongEncoding | 编码错误 |
| 10 | wrongValue | 值错误 |
| 11 | noCreation | 无法创建 |
| 12 | inconsistentValue | 值不一致 |
| 13 | resourceUnavailable | 资源不可用 |
| 14 | commitFailed | 提交失败 |
| 15 | undoFailed | 撤销失败 |
| 16 | authorizationError | 授权错误 |
| 17 | notWritable | 不可写 |
| 18 | inconsistentName | 名称不一致 |

## 实际示例

### 示例 1: SNMPv2c Get 请求

```json
{
  "ndpi": {
    "protocol": "SNMP",
    "snmp": {
      "version": 1,
      "primitive": 0,
      "error_status": 0
    }
  }
}
```

### 示例 2: SNMP Get 响应

```json
{
  "ndpi": {
    "protocol": "SNMP",
    "snmp": {
      "version": 1,
      "primitive": 2,
      "error_status": 0
    }
  }
}
```

### 示例 3: SNMP Set 请求

```json
{
  "ndpi": {
    "protocol": "SNMP",
    "snmp": {
      "version": 1,
      "primitive": 3,
      "error_status": 0
    }
  }
}
```

### 示例 4: SNMP Trap (陷阱)

```json
{
  "ndpi": {
    "protocol": "SNMP",
    "snmp": {
      "version": 1,
      "primitive": 4,
      "error_status": 0
    }
  }
}
```

### 示例 5: SNMP GetBulk 请求

```json
{
  "ndpi": {
    "protocol": "SNMP",
    "snmp": {
      "version": 1,
      "primitive": 5,
      "error_status": 0
    }
  }
}
```

### 示例 6: SNMP 错误响应

```json
{
  "ndpi": {
    "protocol": "SNMP",
    "snmp": {
      "version": 1,
      "primitive": 2,
      "error_status": 2
    }
  }
}
```

## 注意事项

### 1. SNMP 端口

- **UDP 161**: SNMP Agent 监听端口（Get/Set 请求）
- **UDP 162**: SNMP Manager 接收 Trap 的端口

### 2. 社区字符串

- SNMPv1 和 v2c 使用社区字符串作为认证
- 常见默认值：public (只读), private (读写)
- 社区字符串以明文传输，存在安全风险

### 3. SNMPv3 安全性

SNMPv3 提供：
- 用户认证 (Authentication)
- 数据加密 (Privacy)
- 消息完整性验证
- 时间戳防止重放攻击

### 4. MIB (Management Information Base)

- SNMP 使用 MIB 定义管理对象
- OID (Object Identifier) 用于标识具体的管理对象
- 常见 MIB: system, interfaces, ip, tcp, udp

### 5. SNMP Trap

- Trap 是异步通知机制
- 设备主动向管理站发送事件通知
- 不需要管理站轮询

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_SNMP, 行 1680-1686)
- **协议检测**: `src/lib/protocols/snmp.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_SNMP:
  ndpi_serialize_start_of_block(serializer, "snmp");
  ndpi_serialize_string_uint32(serializer, "version", flow->protos.snmp.version);
  ndpi_serialize_string_uint32(serializer, "primitive", flow->protos.snmp.primitive);
  ndpi_serialize_string_uint32(serializer, "error_status", flow->protos.snmp.error_status);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_snmp()` - SNMP 协议检测器

## 参考资料

- [RFC 1157 - Simple Network Management Protocol (SNMP)](https://tools.ietf.org/html/rfc1157)
- [RFC 1905 - Protocol Operations for Version 2 of SNMP](https://tools.ietf.org/html/rfc1905)
- [RFC 3410 - Introduction to SNMPv3](https://tools.ietf.org/html/rfc3410)
- [RFC 3411 - Architecture for SNMP Management Frameworks](https://tools.ietf.org/html/rfc3411)
- [Net-SNMP](http://www.net-snmp.org/)
- [nDPI SNMP 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/snmp.c)

## 常见用例

### 1. 网络设备监控

监控路由器、交换机、服务器的状态和性能指标。

### 2. 流量统计

收集网络接口的流量统计数据。

### 3. 设备配置

远程配置网络设备参数。

### 4. 故障告警

通过 SNMP Trap 接收设备故障通知。

### 5. 资产管理

自动收集网络设备的硬件和软件信息。

### 6. 性能分析

监控 CPU、内存、磁盘等资源使用情况。

### 7. 安全审计

检测未授权的 SNMP 访问和配置更改。
