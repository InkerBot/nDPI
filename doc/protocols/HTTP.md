# HTTP 协议文档

## 协议概述

- **协议全称**: HyperText Transfer Protocol (超文本传输协议)
- **协议 ID**: NDPI_PROTOCOL_HTTP (7), NDPI_PROTOCOL_HTTP_CONNECT (130), NDPI_PROTOCOL_HTTP_PROXY (131)
- **协议类别**: Web 服务
- **传输层**: TCP (默认端口 80, 8080, 8000 等)

## 简介

HTTP 是互联网上应用最广泛的协议，用于 Web 浏览器和服务器之间的通信。nDPI 可以提取 HTTP 请求和响应中的关键信息，包括 URL、状态码、内容类型、User-Agent 等元数据。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "HTTP",
    "hostname": "www.example.com",
    "http": {
      "url": "http://www.example.com/index.html",
      "code": 200,
      "content_type": "text/html",
      "user_agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)",
      "request_content_type": "application/x-www-form-urlencoded",
      "detected_os": "Windows",
      "nat_ip": "192.168.1.100"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| url | string | 完整的请求 URL | "http://example.com/path" |
| code | uint32 | HTTP 响应状态码 | 200 |
| content_type | string | 响应内容类型 (响应头) | "text/html" |
| user_agent | string | 客户端 User-Agent 字符串 | "Mozilla/5.0..." |
| request_content_type | string | 请求内容类型 (请求头) | "application/json" |
| detected_os | string | 检测到的操作系统 | "Windows", "Linux", "macOS" |
| nat_ip | string | NAT 后的真实 IP (从 X-Forwarded-For 等提取) | "192.168.1.100" |

### HTTP 状态码分类

| 范围 | 类别 | 说明 |
|------|------|------|
| 1xx | 信息响应 | 请求已接收，继续处理 |
| 2xx | 成功 | 请求成功被服务器接收、理解并处理 |
| 3xx | 重定向 | 需要后续操作才能完成请求 |
| 4xx | 客户端错误 | 请求包含语法错误或无法完成 |
| 5xx | 服务器错误 | 服务器在处理请求时发生错误 |

### 常见状态码

| 代码 | 含义 |
|------|------|
| 200 | OK - 成功 |
| 301 | Moved Permanently - 永久重定向 |
| 302 | Found - 临时重定向 |
| 304 | Not Modified - 未修改 |
| 400 | Bad Request - 错误请求 |
| 401 | Unauthorized - 未授权 |
| 403 | Forbidden - 禁止访问 |
| 404 | Not Found - 未找到 |
| 500 | Internal Server Error - 服务器内部错误 |
| 502 | Bad Gateway - 错误网关 |
| 503 | Service Unavailable - 服务不可用 |

## 实际示例

### 示例 1: 标准 HTTP GET 请求

```json
{
  "ndpi": {
    "protocol": "HTTP",
    "hostname": "www.example.com",
    "http": {
      "url": "http://www.example.com/page.html",
      "code": 200,
      "content_type": "text/html; charset=UTF-8",
      "user_agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36"
    }
  }
}
```

### 示例 2: HTTP POST 请求

```json
{
  "ndpi": {
    "protocol": "HTTP",
    "hostname": "api.example.com",
    "http": {
      "url": "http://api.example.com/v1/users",
      "code": 201,
      "content_type": "application/json",
      "user_agent": "curl/7.68.0",
      "request_content_type": "application/json"
    }
  }
}
```

### 示例 3: HTTP 404 错误

```json
{
  "ndpi": {
    "protocol": "HTTP",
    "hostname": "www.example.com",
    "http": {
      "url": "http://www.example.com/notfound.html",
      "code": 404,
      "content_type": "text/html",
      "user_agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64)"
    }
  }
}
```

### 示例 4: 包含 NAT IP 信息

```json
{
  "ndpi": {
    "protocol": "HTTP",
    "hostname": "www.example.com",
    "http": {
      "url": "http://www.example.com/",
      "code": 200,
      "content_type": "text/html",
      "user_agent": "Mozilla/5.0 (iPhone; CPU iPhone OS 14_0)",
      "detected_os": "iOS",
      "nat_ip": "10.0.1.50"
    }
  }
}
```

### 示例 5: 图片资源请求

```json
{
  "ndpi": {
    "protocol": "HTTP",
    "hostname": "cdn.example.com",
    "http": {
      "url": "http://cdn.example.com/images/logo.png",
      "code": 200,
      "content_type": "image/png",
      "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7)"
    }
  }
}
```

### 示例 6: API 请求

```json
{
  "ndpi": {
    "protocol": "HTTP",
    "hostname": "api.github.com",
    "http": {
      "url": "http://api.github.com/users/octocat",
      "code": 200,
      "content_type": "application/json; charset=utf-8",
      "user_agent": "GitHub-Client/1.0",
      "request_content_type": "application/json"
    }
  }
}
```

## 注意事项

### 1. 字段可能为空

- 如果 `url` 为 NULL，则 HTTP 块中可能不包含任何字段
- `detected_os` 和 `nat_ip` 仅在能够提取时才出现
- `request_content_type` 仅在 POST/PUT 等包含请求体的方法中出现

### 2. HTTPS vs HTTP

- 本文档描述的是明文 HTTP 流量
- HTTPS (HTTP over TLS) 流量会被识别为 TLS 协议
- 对于 HTTPS，只能提取 TLS 握手信息，无法获取 URL 等应用层数据

### 3. HTTP 协议变体

- `NDPI_PROTOCOL_HTTP` (7) - 标准 HTTP
- `NDPI_PROTOCOL_HTTP_CONNECT` (130) - HTTP CONNECT 代理方法
- `NDPI_PROTOCOL_HTTP_PROXY` (131) - HTTP 代理流量

### 4. User-Agent 检测

- nDPI 可以从 User-Agent 推断操作系统和浏览器
- `detected_os` 字段包含推断结果
- 可能的值：Windows, Linux, macOS, iOS, Android 等

### 5. Content-Type 重要性

- `content_type` 帮助识别资源类型
- 常见类型：
  - `text/html` - HTML 页面
  - `application/json` - JSON 数据
  - `image/jpeg`, `image/png` - 图片
  - `video/mp4` - 视频
  - `application/javascript` - JavaScript 代码

### 6. NAT IP 提取

- 从 HTTP 头部提取：
  - `X-Forwarded-For`
  - `X-Real-IP`
  - `True-Client-IP`
- 用于追踪 NAT 后的真实客户端 IP

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_HTTP, 行 1710-1735)
- **协议检测**: `src/lib/protocols/http.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_HTTP:
case NDPI_PROTOCOL_HTTP_CONNECT:
case NDPI_PROTOCOL_HTTP_PROXY:
  ndpi_serialize_start_of_block(serializer, "http");

  if(flow->http.url != NULL) {
    ndpi_serialize_string_string(serializer, "url", flow->http.url);
    ndpi_serialize_string_uint32(serializer, "code", flow->http.response_status_code);
    ndpi_serialize_string_string(serializer, "content_type", flow->http.content_type);
    ndpi_serialize_string_string(serializer, "user_agent", flow->http.user_agent);
  }

  if (flow->http.request_content_type != NULL)
    ndpi_serialize_string_string(serializer, "request_content_type",
                                 flow->http.request_content_type);

  if (flow->http.detected_os != NULL)
    ndpi_serialize_string_string(serializer, "detected_os",
                                 flow->http.detected_os);

  if (flow->http.nat_ip != NULL)
    ndpi_serialize_string_string(serializer, "nat_ip",
                                 flow->http.nat_ip);

  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_http()` - HTTP 协议检测器
- `ndpi_parse_http_headers()` - 解析 HTTP 头部

### HTTP 信息提取

- `ndpi_http_parse_url()` - 解析 URL
- `ndpi_http_parse_content_type()` - 解析 Content-Type
- `ndpi_http_extract_user_agent()` - 提取 User-Agent

## 参考资料

- [RFC 2616 - Hypertext Transfer Protocol -- HTTP/1.1](https://tools.ietf.org/html/rfc2616)
- [RFC 7230 - HTTP/1.1: Message Syntax and Routing](https://tools.ietf.org/html/rfc7230)
- [RFC 7231 - HTTP/1.1: Semantics and Content](https://tools.ietf.org/html/rfc7231)
- [HTTP Status Code Definitions](https://httpstatuses.com/)
- [nDPI HTTP 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/http.c)

## 常见用例

### 1. Web 流量分析

分析用户访问的网站、页面和资源类型。

### 2. 应用识别

基于 User-Agent 和 URL 模式识别具体应用。

### 3. 内容过滤

根据 URL 和 Content-Type 实现内容过滤策略。

### 4. 性能监控

监控 HTTP 响应状态码，识别错误和性能问题。

### 5. 安全审计

检测异常的 HTTP 请求模式，识别潜在的安全威胁。

### 6. 广告屏蔽

识别广告相关的 HTTP 请求并进行过滤。

### 7. 带宽管理

根据 Content-Type 对不同类型的流量进行 QoS 管理。

## HTTP/2 和 HTTP/3

- HTTP/2 运行在 TLS 上，会被识别为 TLS 协议
- HTTP/3 运行在 QUIC 上，会被识别为 QUIC 协议
- 参见 [TLS.md](TLS.md) 和 [QUIC.md](QUIC.md) 获取更多信息
