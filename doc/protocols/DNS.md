# DNS 协议文档

## 协议概述

- **协议全称**: Domain Name System (域名系统)
- **协议 ID**: NDPI_PROTOCOL_DNS (5)
- **协议类别**: 网络服务
- **传输层**: UDP/TCP (默认端口 53)

## 简介

DNS 是互联网的核心服务之一，用于将人类可读的域名（如 www.example.com）解析为机器可识别的 IP 地址。nDPI 能够深度检测 DNS 流量并提取查询和响应信息，包括查询类型、响应代码、查询的域名列表以及解析结果。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "DNS",
    "dns": {
      "num_queries": 1,
      "num_answers": 1,
      "reply_code": 0,
      "query_type": 1,
      "rsp_type": 1,
      "rsp_addr": [
        {
          "addr": "93.184.216.34,ttl=86400"
        }
      ]
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| num_queries | uint32 | DNS 查询数量 | 1 |
| num_answers | uint32 | DNS 应答数量 | 1 |
| reply_code | uint32 | DNS 响应代码 | 0 |
| query_type | uint32 | 查询类型 | 1 |
| rsp_type | uint32 | 响应类型 | 1 |
| rsp_addr | array | 解析结果地址列表（带 TTL） | ["93.184.216.34,ttl=86400"] |

### DNS 响应代码 (reply_code)

| 代码 | 含义 | 说明 |
|------|------|------|
| 0 | NOERROR | 查询成功，无错误 |
| 1 | FORMERR | 格式错误 |
| 2 | SERVFAIL | 服务器失败 |
| 3 | NXDOMAIN | 域名不存在 |
| 4 | NOTIMP | 不支持的查询类型 |
| 5 | REFUSED | 查询被拒绝 |

### DNS 查询类型 (query_type)

| 类型 | 值 | 说明 |
|------|-----|------|
| A | 1 | IPv4 地址记录 |
| NS | 2 | 名称服务器记录 |
| CNAME | 5 | 规范名称记录 |
| SOA | 6 | 授权起始记录 |
| PTR | 12 | 指针记录 |
| MX | 15 | 邮件交换记录 |
| TXT | 16 | 文本记录 |
| AAAA | 28 | IPv6 地址记录 |
| SRV | 33 | 服务记录 |
| ANY | 255 | 所有记录 |

## 实际示例

### 示例 1: A 记录查询 (IPv4)

```json
{
  "ndpi": {
    "protocol": "DNS",
    "hostname": "www.google.com",
    "dns": {
      "num_queries": 1,
      "num_answers": 1,
      "reply_code": 0,
      "query_type": 1,
      "rsp_type": 1,
      "rsp_addr": [
        {
          "addr": "142.250.185.132,ttl=300"
        }
      ]
    }
  }
}
```

### 示例 2: AAAA 记录查询 (IPv6)

```json
{
  "ndpi": {
    "protocol": "DNS",
    "hostname": "www.example.com",
    "dns": {
      "num_queries": 1,
      "num_answers": 1,
      "reply_code": 0,
      "query_type": 28,
      "rsp_type": 28,
      "rsp_addr": [
        {
          "addr": "2606:2800:220:1:248:1893:25c8:1946,ttl=86400"
        }
      ]
    }
  }
}
```

### 示例 3: 多个 A 记录应答

```json
{
  "ndpi": {
    "protocol": "DNS",
    "hostname": "www.cloudflare.com",
    "dns": {
      "num_queries": 1,
      "num_answers": 2,
      "reply_code": 0,
      "query_type": 1,
      "rsp_type": 1,
      "rsp_addr": [
        {
          "addr": "104.16.132.229,ttl=300"
        },
        {
          "addr": "104.16.133.229,ttl=300"
        }
      ]
    }
  }
}
```

### 示例 4: 域名不存在 (NXDOMAIN)

```json
{
  "ndpi": {
    "protocol": "DNS",
    "hostname": "nonexistent.example.com",
    "dns": {
      "num_queries": 1,
      "num_answers": 0,
      "reply_code": 3,
      "query_type": 1,
      "rsp_type": 1,
      "rsp_addr": []
    }
  }
}
```

## 注意事项

### 1. TTL 值

- `rsp_addr` 数组中的每个地址都附带 TTL（生存时间）值
- TTL 格式：`"IP地址,ttl=秒数"`
- TTL 表示该记录可以被缓存的时间

### 2. IPv4 vs IPv6

- IPv4 地址：query_type = 1 (A 记录)
- IPv6 地址：query_type = 28 (AAAA 记录)
- nDPI 自动识别响应中的地址类型

### 3. 空响应

- 如果查询失败或域名不存在，`rsp_addr` 数组为空
- 检查 `reply_code` 字段以确定失败原因

### 4. 多次查询

- `num_queries` 可能大于 1，表示一个 DNS 请求包含多个查询
- `num_answers` 表示收到的应答记录数量

### 5. 主机名字段

- `hostname` 字段包含查询的域名
- 此字段从 DNS 查询包中提取
- 如果无法提取，该字段可能不存在

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_DNS, 行 1597-1625)
- **协议检测**: `src/lib/protocols/dns.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_DNS:
  ndpi_serialize_start_of_block(serializer, "dns");
  ndpi_serialize_string_uint32(serializer, "num_queries", flow->protos.dns.num_queries);
  ndpi_serialize_string_uint32(serializer, "num_answers", flow->protos.dns.num_answers);
  ndpi_serialize_string_uint32(serializer, "reply_code",  flow->protos.dns.reply_code);
  ndpi_serialize_string_uint32(serializer, "query_type",  flow->protos.dns.query_type);
  ndpi_serialize_string_uint32(serializer, "rsp_type",    flow->protos.dns.rsp_type);

  ndpi_serialize_start_of_list(serializer, "rsp_addr");
  for(i=0; i<flow->protos.dns.num_rsp_addr; i++) {
    // 序列化每个响应地址和 TTL
  }
  ndpi_serialize_end_of_list(serializer);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_dns()` - DNS 协议检测器
- `ndpi_check_flow_func()` - 流量检测入口

### 序列化函数

- `ndpi_dpi2json()` - 主序列化函数
- `ndpi_serialize_start_of_block()` - 开始 JSON 块
- `ndpi_serialize_string_uint32()` - 序列化整数字段
- `ndpi_serialize_start_of_list()` - 开始 JSON 数组

## 参考资料

- [RFC 1035 - Domain Names - Implementation and Specification](https://tools.ietf.org/html/rfc1035)
- [RFC 3596 - DNS Extensions to Support IP Version 6](https://tools.ietf.org/html/rfc3596)
- [RFC 6895 - Domain Name System (DNS) IANA Considerations](https://tools.ietf.org/html/rfc6895)
- [nDPI DNS 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/dns.c)
- [nDPI 主页](https://www.ntop.org/products/deep-packet-inspection/ndpi/)

## 常见用例

### 1. DNS 流量分析

监控和分析网络中的 DNS 查询模式，识别异常查询行为。

### 2. 域名过滤

基于 DNS 查询内容实现内容过滤和访问控制。

### 3. DGA 检测

检测使用域名生成算法 (Domain Generation Algorithm) 的恶意软件。

### 4. DNS 隧道检测

识别通过 DNS 协议进行数据渗透的异常流量。

### 5. 性能监控

监控 DNS 响应时间和失败率，评估 DNS 服务质量。
