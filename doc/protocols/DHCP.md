# DHCP 协议文档

## 协议概述

- **协议全称**: Dynamic Host Configuration Protocol (动态主机配置协议)
- **协议 ID**: NDPI_PROTOCOL_DHCP (18)
- **协议类别**: 网络服务
- **传输层**: UDP (端口 67/68)

## 简介

DHCP 是一种网络管理协议，用于自动分配 IP 地址和其他网络配置参数给网络中的设备。nDPI 可以提取 DHCP 流量中的设备指纹和类标识符信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "DHCP",
    "dhcp": {
      "fingerprint": "1,3,6,15,28,33",
      "class_ident": "MSFT 5.0"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| fingerprint | string | DHCP 指纹（请求的选项列表） | "1,3,6,15,28,33" |
| class_ident | string | 供应商类标识符 (Vendor Class Identifier) | "MSFT 5.0" |

### DHCP 指纹

DHCP 指纹是基于客户端请求的 DHCP 选项顺序生成的字符串，可用于：
- 设备类型识别
- 操作系统指纹识别
- 网络设备分类

### 常见的 DHCP 选项

| 选项 | 名称 | 说明 |
|------|------|------|
| 1 | Subnet Mask | 子网掩码 |
| 3 | Router | 默认网关 |
| 6 | Domain Name Server | DNS 服务器 |
| 15 | Domain Name | 域名 |
| 28 | Broadcast Address | 广播地址 |
| 33 | Static Route | 静态路由 |
| 42 | NTP Servers | NTP 服务器 |
| 51 | IP Address Lease Time | IP 地址租期 |

## 实际示例

### 示例 1: Windows 10 设备

```json
{
  "ndpi": {
    "protocol": "DHCP",
    "dhcp": {
      "fingerprint": "1,3,6,15,31,33,43,44,46,47,121,249,252",
      "class_ident": "MSFT 5.0"
    }
  }
}
```

### 示例 2: macOS 设备

```json
{
  "ndpi": {
    "protocol": "DHCP",
    "dhcp": {
      "fingerprint": "1,3,6,15,119,252",
      "class_ident": "Apple"
    }
  }
}
```

### 示例 3: Linux 设备

```json
{
  "ndpi": {
    "protocol": "DHCP",
    "dhcp": {
      "fingerprint": "1,28,2,3,15,6,119,12,44,47,26,121,42",
      "class_ident": "dhcpcd-6.11.5"
    }
  }
}
```

### 示例 4: Android 设备

```json
{
  "ndpi": {
    "protocol": "DHCP",
    "dhcp": {
      "fingerprint": "1,3,6,15,26,28,51,58,59",
      "class_ident": "android-dhcp-11"
    }
  }
}
```

### 示例 5: iOS 设备

```json
{
  "ndpi": {
    "protocol": "DHCP",
    "dhcp": {
      "fingerprint": "1,3,6,15,119,252",
      "class_ident": "iOS"
    }
  }
}
```

## 注意事项

### 1. 指纹唯一性

- DHCP 指纹不是绝对唯一的
- 同一操作系统的不同版本可能有相同指纹
- 某些设备可能不发送完整的选项列表

### 2. 类标识符

- `class_ident` 字段包含厂商提供的标识信息
- 不同厂商和软件使用不同的格式
- 该字段可能为空

### 3. 隐私考虑

- DHCP 指纹可用于设备追踪
- 某些操作系统会随机化 DHCP 选项以保护隐私

### 4. DHCPv6

- DHCPv6 使用不同的协议 (NDPI_PROTOCOL_DHCPV6 = 103)
- DHCPv6 的字段结构可能不同

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_DHCP, 行 1562-1567)
- **协议检测**: `src/lib/protocols/dhcp.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_DHCP:
  ndpi_serialize_start_of_block(serializer, "dhcp");
  ndpi_serialize_string_string(serializer, "fingerprint", flow->protos.dhcp.fingerprint);
  ndpi_serialize_string_string(serializer, "class_ident", flow->protos.dhcp.class_ident);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_dhcp()` - DHCP 协议检测器
- `ndpi_dhcp_extract_fingerprint()` - 提取 DHCP 指纹

## 参考资料

- [RFC 2131 - Dynamic Host Configuration Protocol](https://tools.ietf.org/html/rfc2131)
- [RFC 2132 - DHCP Options and BOOTP Vendor Extensions](https://tools.ietf.org/html/rfc2132)
- [RFC 8415 - Dynamic Host Configuration Protocol for IPv6 (DHCPv6)](https://tools.ietf.org/html/rfc8415)
- [DHCP Fingerprinting](https://github.com/fingerprintjs/blog-dhcp-fingerprint)
- [nDPI DHCP 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/dhcp.c)

## 常见用例

### 1. 设备识别

通过 DHCP 指纹和类标识符识别网络中的设备类型。

### 2. 网络资产管理

自动发现和分类网络中的设备。

### 3. 安全监控

检测未授权设备接入网络。

### 4. IoT 设备管理

识别和管理物联网设备。

### 5. BYOD 策略

识别员工自带设备并应用相应策略。
