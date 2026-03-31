# NAT-PMP 协议文档

## 协议概述

- **协议全称**: NAT Port Mapping Protocol (NAT 端口映射协议)
- **协议 ID**: NDPI_PROTOCOL_NATPMP (312)
- **协议类别**: 网络地址转换协议
- **传输层**: UDP (端口 5351)

## 简介

NAT-PMP 是一种用于在 NAT 网关上自动配置端口转发的协议，由 Apple 开发。它允许内网设备请求 NAT 网关创建端口映射，使外部可以访问内网服务。NAT-PMP 被设计为 UPnP IGD (Internet Gateway Device) 的更简单替代方案。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "NATPMP",
    "natpmp": {
      "result": 0,
      "internal_port": 8080,
      "external_port": 8080,
      "external_address": "203.0.113.100"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| result | uint32 | 操作结果代码 | 0 |
| internal_port | uint32 | 内网端口号 | 8080 |
| external_port | uint32 | 外网端口号 | 8080 |
| external_address | string | 外网 IP 地址 | "203.0.113.100" |

### 结果代码

| 代码 | 含义 |
|------|------|
| 0 | Success (成功) |
| 1 | Unsupported Version (不支持的版本) |
| 2 | Not Authorized (未授权) |
| 3 | Network Failure (网络故障) |
| 4 | Out of Resources (资源不足) |
| 5 | Unsupported Opcode (不支持的操作码) |

## 实际示例

### 示例 1: 成功的端口映射

```json
{
  "ndpi": {
    "protocol": "NATPMP",
    "natpmp": {
      "result": 0,
      "internal_port": 8080,
      "external_port": 8080,
      "external_address": "203.0.113.50"
    }
  }
}
```

### 示例 2: 外部 IP 地址查询

```json
{
  "ndpi": {
    "protocol": "NATPMP",
    "natpmp": {
      "result": 0,
      "internal_port": 0,
      "external_port": 0,
      "external_address": "198.51.100.25"
    }
  }
}
```

### 示例 3: 游戏端口映射

```json
{
  "ndpi": {
    "protocol": "NATPMP",
    "natpmp": {
      "result": 0,
      "internal_port": 27015,
      "external_port": 27015,
      "external_address": "203.0.113.200"
    }
  }
}
```

### 示例 4: 操作失败

```json
{
  "ndpi": {
    "protocol": "NATPMP",
    "natpmp": {
      "result": 4,
      "internal_port": 0,
      "external_port": 0,
      "external_address": "0.0.0.0"
    }
  }
}
```

## 注意事项

### 1. NAT-PMP 端口

- **UDP 5351** - 默认端口
- 网关在此端口监听请求
- 客户端从临时端口发送

### 2. NAT-PMP 操作

NAT-PMP 支持两种主要操作：

**1. 查询外部地址**：
- 获取 NAT 网关的外网 IP
- 不涉及端口映射

**2. 端口映射请求**：
- 创建或删除端口映射
- 指定内外网端口
- 设置映射生存时间

### 3. 端口映射生存时间

- 映射不是永久的
- 请求中指定生存时间（秒）
- 客户端需要定期刷新映射
- 生存时间为 0 表示删除映射

### 4. NAT-PMP vs UPnP IGD

**NAT-PMP 优势**：
- 更简单的协议
- 更容易实现
- 更少的安全漏洞
- 更可靠

**UPnP IGD**：
- 功能更丰富
- 支持更复杂的场景
- 更广泛的设备支持
- 协议较复杂

### 5. PCP (Port Control Protocol)

PCP 是 NAT-PMP 的继任者：
- IETF 标准化版本
- 向后兼容 NAT-PMP
- 支持 IPv6
- 更多功能（如多个映射）

### 6. 安全考虑

**潜在风险**：
- 恶意软件可能创建后门
- 未授权的端口暴露
- 绕过防火墙策略

**防护措施**：
- 监控 NAT-PMP 活动
- 限制可映射的端口
- 审计端口映射
- 考虑禁用（如不需要）

### 7. 使用场景

NAT-PMP 主要用于：
- P2P 应用（BitTorrent、游戏等）
- VoIP 应用
- 远程桌面
- 家庭服务器
- IoT 设备

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_NATPMP, 行 1662-1670)
- **协议检测**: `src/lib/protocols/natpmp.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_NATPMP:
  ndpi_serialize_start_of_block(serializer, "natpmp");
  ndpi_serialize_string_uint32(serializer, "result", flow->protos.natpmp.result_code);
  ndpi_serialize_string_uint32(serializer, "internal_port", flow->protos.natpmp.internal_port);
  ndpi_serialize_string_uint32(serializer, "external_port", flow->protos.natpmp.external_port);
  inet_ntop(AF_INET, &flow->protos.natpmp.external_address.ipv4, buf, sizeof(buf));
  ndpi_serialize_string_string(serializer, "external_address", buf);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_natpmp()` - NAT-PMP 协议检测器

## 参考资料

- [RFC 6886 - NAT Port Mapping Protocol (NAT-PMP)](https://tools.ietf.org/html/rfc6886)
- [RFC 6887 - Port Control Protocol (PCP)](https://tools.ietf.org/html/rfc6887)
- [Apple Bonjour NAT-PMP](https://developer.apple.com/bonjour/)
- [nDPI NAT-PMP 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/natpmp.c)

## 常见用例

### 1. P2P 文件共享

BitTorrent 客户端自动打开端口提高连接性。

### 2. 游戏

多人在线游戏自动配置端口转发。

### 3. VoIP 应用

Skype、FaceTime 等使用 NAT-PMP 改善连接。

### 4. 远程访问

远程桌面应用请求端口映射。

### 5. 家庭服务器

运行在家庭网络的服务器（Web、文件服务器等）。

### 6. IoT 设备

智能家居设备的远程访问。

### 7. 媒体流

媒体服务器和流媒体应用。

## NAT-PMP 工作流程

典型的端口映射流程：

```
1. 客户端发现网关
   - 向 224.0.0.1:5351 发送组播（或直连网关）

2. 查询外部地址
   - 发送外部地址请求
   - 网关返回公网 IP

3. 请求端口映射
   - 指定内外网端口
   - 指定协议（TCP/UDP）
   - 指定生存时间

4. 网关响应
   - 返回结果代码
   - 返回分配的外网端口
   - 返回实际生存时间

5. 维持映射
   - 定期刷新映射（生存时间过半时）
   - 应用退出时删除映射
```

## NAT-PMP 消息格式

### 请求消息

```
+-----+-----+----------+
|  0  | Op  | Data     |
+-----+-----+----------+
 版本  操作码  操作数据
```

### 响应消息

```
+-----+-----+--------+------------+
|  0  | Op  | Result | Response   |
+-----+-----+--------+------------+
 版本  操作码  结果码   响应数据
```

## 网关支持

支持 NAT-PMP 的常见路由器和网关：
- Apple AirPort 系列
- 许多基于 Linux 的路由器（OpenWrt、DD-WRT）
- macOS 互联网共享
- 某些商用路由器固件

## 客户端库

NAT-PMP 客户端库：
- **libnatpmp** - C 库
- **miniupnpc** - 同时支持 NAT-PMP 和 UPnP
- 各语言的绑定（Python、Java、Go 等）

## 故障排查

### 常见问题

1. **网关不响应**
   - 检查网关是否支持 NAT-PMP
   - 验证网关地址
   - 检查防火墙设置

2. **端口映射失败**
   - 端口可能已被占用
   - 资源限制（映射数量）
   - 权限问题

3. **映射不稳定**
   - 网关重启导致映射丢失
   - 生存时间设置过短
   - 网络不稳定

### 调试方法

```bash
# 使用 natpmpc 工具测试
natpmpc -a 8080 8080 tcp 3600

# 查询外部地址
natpmpc -g

# 监控 NAT-PMP 流量
tcpdump -i any -n port 5351
```

## 安全建议

1. **监控端口映射**
   - 记录所有映射请求
   - 审计意外的映射

2. **限制映射**
   - 限制可映射的端口范围
   - 限制映射数量

3. **访问控制**
   - 仅允许内网设备请求映射
   - 考虑设备白名单

4. **定期审计**
   - 检查活动的端口映射
   - 删除不必要的映射

## 相关协议

- [UPnP](https://en.wikipedia.org/wiki/Universal_Plug_and_Play) - 类似功能的协议
- [PCP](https://tools.ietf.org/html/rfc6887) - NAT-PMP 的继任者
- [STUN.md](STUN.md) - NAT 穿越协议
- [DHCP.md](DHCP.md) - 网络配置协议
