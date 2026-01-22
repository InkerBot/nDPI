# ndpi_dpi2json 函数使用指南

## 概述

`ndpi_dpi2json` 函数是 nDPI 库中用于将深度包检测 (DPI) 结果序列化为 JSON 格式的核心函数。该函数能够提取网络流的协议信息、元数据和其他相关字段，并以结构化的 JSON 格式输出。

## 函数签名

```c
int ndpi_dpi2json(struct ndpi_detection_module_struct *ndpi_struct,
                  struct ndpi_flow_struct *flow,
                  ndpi_protocol l7_protocol,
                  ndpi_serializer *serializer);
```

### 参数说明

- `ndpi_struct`: nDPI 检测模块结构体指针
- `flow`: 网络流结构体指针，包含检测到的协议信息
- `l7_protocol`: 第七层协议标识符
- `serializer`: JSON 序列化器对象

### 返回值

- 成功返回 0
- 失败返回 -1

## 基本使用流程

### 1. 初始化序列化器

```c
ndpi_serializer serializer;
ndpi_init_serializer(&serializer, ndpi_serialization_format_json);
```

### 2. 调用 ndpi_dpi2json

```c
ndpi_dpi2json(ndpi_struct, flow, l7_protocol, &serializer);
```

### 3. 获取 JSON 输出

```c
char *json_output = ndpi_serializer_get_buffer(&serializer, NULL);
printf("%s\n", json_output);
```

### 4. 清理资源

```c
ndpi_term_serializer(&serializer);
```

## JSON 输出结构

所有协议的 JSON 输出都遵循以下基本结构：

```json
{
  "ndpi": {
    "protocol": "协议名称",
    "hostname": "主机名（如果可用）",
    "domainame": "域名（如果可用）",
    "协议特定字段": {
      // 各协议特有的字段
    }
  }
}
```

## 通用字段说明

### protocol

- **类型**: string
- **说明**: 检测到的应用层协议名称
- **示例**: "DNS", "HTTP", "TLS", "SSH"

### hostname

- **类型**: string
- **说明**: 从流量中提取的主机名或服务器名称
- **示例**: "www.example.com"
- **注意**: 并非所有协议都包含此字段

### domainame

- **类型**: string
- **说明**: 从主机名中提取的域名
- **示例**: "example.com"
- **注意**: 需要先有 hostname 字段

### stream_content

- **类型**: string
- **说明**: 流媒体类型标识
- **可能值**: "audio", "video", "audio+video", "unknown"
- **注意**: 仅在检测到流媒体流量时出现

## 支持的协议

nDPI 支持数百种协议的深度检测和 JSON 序列化。以下是主要协议分类：

### 核心网络协议

- [DNS](protocols/DNS.md) - 域名系统
- [HTTP](protocols/HTTP.md) - 超文本传输协议
- [DHCP](protocols/DHCP.md) - 动态主机配置协议
- [NTP](protocols/NTP.md) - 网络时间协议
- [MDNS](protocols/MDNS.md) - 多播 DNS
- [SNMP](protocols/SNMP.md) - 简单网络管理协议

### 安全协议

- [TLS](protocols/TLS.md) - 传输层安全协议
- [DTLS](protocols/DTLS.md) - 数据报传输层安全协议
- [SSH](protocols/SSH.md) - 安全外壳协议
- [Kerberos](protocols/Kerberos.md) - 网络认证协议

### 传输协议

- [QUIC](protocols/QUIC.md) - 快速 UDP 互联网连接

### 邮件协议

- [SMTP](protocols/SMTP.md) - 简单邮件传输协议
- [IMAP](protocols/IMAP.md) - 互联网邮件访问协议
- [POP3](protocols/POP3.md) - 邮局协议版本 3

### 文件传输协议

- [FTP](protocols/FTP.md) - 文件传输协议
- [BitTorrent](protocols/BitTorrent.md) - BitTorrent 点对点协议

### VoIP 协议

- [SIP](protocols/SIP.md) - 会话初始协议
- [STUN](protocols/STUN.md) - NAT 会话穿越实用工具

### 其他协议

- [MongoDB](protocols/MongoDB.md) - MongoDB 数据库协议
- [Telnet](protocols/Telnet.md) - 远程终端协议
- [NATPMP](protocols/NATPMP.md) - NAT 端口映射协议

完整的协议列表和详细文档请参见 [协议文档索引](protocols/README.md)。

## 使用示例

### 示例 1: 检测并序列化 DNS 流量

```c
#include "ndpi_api.h"

void process_dns_flow(struct ndpi_detection_module_struct *ndpi_struct,
                      struct ndpi_flow_struct *flow,
                      ndpi_protocol l7_protocol) {
    ndpi_serializer serializer;
    
    // 初始化序列化器
    ndpi_init_serializer(&serializer, ndpi_serialization_format_json);
    
    // 序列化为 JSON
    if(ndpi_dpi2json(ndpi_struct, flow, l7_protocol, &serializer) == 0) {
        char *json_str = ndpi_serializer_get_buffer(&serializer, NULL);
        printf("DNS Query Info: %s\n", json_str);
    }
    
    // 清理
    ndpi_term_serializer(&serializer);
}
```

### 示例 2: 批量处理多个流

```c
void process_multiple_flows(struct ndpi_detection_module_struct *ndpi_struct,
                           struct ndpi_flow_struct **flows,
                           int num_flows) {
    for(int i = 0; i < num_flows; i++) {
        ndpi_serializer serializer;
        ndpi_protocol l7_proto;
        
        // 获取协议信息
        l7_proto = ndpi_detection_giveup(ndpi_struct, flows[i]);
        
        // 序列化
        ndpi_init_serializer(&serializer, ndpi_serialization_format_json);
        ndpi_dpi2json(ndpi_struct, flows[i], l7_proto, &serializer);
        
        // 输出 JSON
        char *json_output = ndpi_serializer_get_buffer(&serializer, NULL);
        printf("Flow %d: %s\n", i, json_output);
        
        ndpi_term_serializer(&serializer);
    }
}
```

## 最佳实践

### 1. 内存管理

- 始终在使用完序列化器后调用 `ndpi_term_serializer()` 释放资源
- 序列化器返回的字符串指针在 `ndpi_term_serializer()` 调用后将失效
- 如需保存 JSON 字符串，应在清理前复制一份

### 2. 性能优化

- 对于高流量场景，可以复用序列化器对象
- 使用 `ndpi_reset_serializer()` 重置序列化器而不是重新创建

```c
ndpi_serializer serializer;
ndpi_init_serializer(&serializer, ndpi_serialization_format_json);

for(int i = 0; i < num_flows; i++) {
    ndpi_reset_serializer(&serializer);
    ndpi_dpi2json(ndpi_struct, flows[i], l7_proto, &serializer);
    // 处理 JSON 输出
}

ndpi_term_serializer(&serializer);
```

### 3. 错误处理

- 检查 `ndpi_dpi2json()` 的返回值
- 确保 flow 指针不为 NULL
- 验证 JSON 输出的有效性

```c
if(flow == NULL) {
    fprintf(stderr, "Error: flow is NULL\n");
    return -1;
}

if(ndpi_dpi2json(ndpi_struct, flow, l7_protocol, &serializer) != 0) {
    fprintf(stderr, "Error: failed to serialize flow\n");
    return -1;
}
```

### 4. 协议特定处理

不同协议的输出字段差异很大，建议：

- 解析 JSON 前先检查协议类型
- 对关键字段做空值检查
- 参考各协议的详细文档了解字段含义

## 常见问题

### Q1: 为什么某些字段为空？

**A**: 可能的原因：
- 流量不完整，未捕获到包含该信息的数据包
- 协议加密，无法提取明文信息
- 协议变体不包含该字段

### Q2: 如何获取更详细的协议信息？

**A**: 
- 确保捕获完整的会话流量
- 检查 nDPI 配置选项（如 SSH 的 hassh 指纹功能）
- 某些信息可能需要启用特定的检测选项

### Q3: JSON 输出格式可以自定义吗？

**A**: 
- 基本结构由 nDPI 定义
- 可以在获取 JSON 字符串后进行后处理
- 可以修改源码实现自定义序列化

### Q4: 支持哪些序列化格式？

**A**: 
nDPI 序列化器支持多种格式：
- `ndpi_serialization_format_json` - JSON 格式（最常用）
- `ndpi_serialization_format_csv` - CSV 格式
- `ndpi_serialization_format_tlv` - TLV (Type-Length-Value) 格式

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (函数 `ndpi_dpi2json`, 约 1530-2016 行)
- **API 声明**: `src/include/ndpi_api.h`
- **协议 ID**: `src/include/ndpi_protocol_ids.h`
- **协议检测**: `src/lib/protocols/*.c`

## 相关 API 函数

### ndpi_init_serializer

初始化序列化器对象。

```c
int ndpi_init_serializer(ndpi_serializer *serializer, 
                        ndpi_serialization_format fmt);
```

### ndpi_term_serializer

释放序列化器资源。

```c
void ndpi_term_serializer(ndpi_serializer *serializer);
```

### ndpi_reset_serializer

重置序列化器以便复用。

```c
void ndpi_reset_serializer(ndpi_serializer *serializer);
```

### ndpi_serializer_get_buffer

获取序列化后的字符串。

```c
char* ndpi_serializer_get_buffer(ndpi_serializer *serializer, 
                                 uint32_t *buffer_len);
```

## 参考资料

- [nDPI 官方文档](https://www.ntop.org/guides/ndpi/)
- [nDPI GitHub 仓库](https://github.com/ntop/nDPI)
- [协议文档索引](protocols/README.md)

## 更新日志

- 2024-01: 初始版本创建
