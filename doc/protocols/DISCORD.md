# Discord 协议文档

## 协议概述

- **协议全称**: Discord Communication Protocol (Discord 通信协议)
- **协议 ID**: NDPI_PROTOCOL_DISCORD (58)
- **协议类别**: 即时通讯和VoIP协议
- **传输层**: TCP/UDP (多种端口，主要使用 HTTPS/WSS)

## 简介

Discord 是一个流行的即时通讯和 VoIP 平台，最初为游戏玩家设计，现已广泛用于各种社区。Discord 使用 HTTPS 和 WebSocket 进行文本通信，使用 WebRTC 进行语音和视频通话。nDPI 可以检测 Discord 流量并提取客户端 IP 信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "Discord",
    "discord": {
      "client_ip": "192.168.1.100"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| client_ip | string | Discord 客户端 IP 地址 | "192.168.1.100" |

### 注意

当 Discord 流量通过 TLS 加密时，`discord` 块可能不出现，流量会被识别为 TLS 协议。

## 实际示例

### 示例 1: Discord 客户端连接

```json
{
  "ndpi": {
    "protocol": "Discord",
    "discord": {
      "client_ip": "10.0.1.50"
    }
  }
}
```

### 示例 2: Discord 语音通话

```json
{
  "ndpi": {
    "protocol": "Discord",
    "discord": {
      "client_ip": "192.168.100.25"
    }
  }
}
```

### 示例 3: 企业 Discord 服务器

```json
{
  "ndpi": {
    "protocol": "Discord",
    "discord": {
      "client_ip": "172.16.5.123"
    }
  }
}
```

## 注意事项

### 1. Discord 架构

Discord 使用多种技术：
- **REST API**: 消息发送、用户管理
- **WebSocket**: 实时事件推送
- **WebRTC**: 语音和视频通话
- **CDN**: 媒体内容分发

### 2. 使用的端口和协议

Discord 主要端口：
- **TCP 443**: HTTPS/WSS（主要通信）
- **UDP 50000-65535**: WebRTC 语音/视频
- **TCP 80**: HTTP（备用）

### 3. Discord 服务器

Discord 域名和服务：
- **discord.com**: 主网站
- **gateway.discord.gg**: WebSocket 网关
- **cdn.discordapp.com**: 内容分发
- **voice.discord.gg**: 语音服务器
- **media.discordapp.net**: 媒体服务

### 4. 功能特点

**文本通信**：
- 频道（文本/语音）
- 私聊和群聊
- 富文本消息
- 文件分享
- 嵌入内容

**语音/视频**：
- 低延迟语音
- 视频通话
- 屏幕共享
- 活动流

**社区功能**：
- 服务器（Guilds）
- 角色和权限
- Bot 集成
- Webhook

### 5. 加密和安全

Discord 安全特性：
- TLS/SSL 加密传输
- WebRTC 媒体加密
- 两因素认证 (2FA)
- IP 地址隐私保护
- 内容审核工具

### 6. 企业使用

Discord 在企业中的应用：
- 团队协作
- 项目沟通
- 社区管理
- 客户支持
- 在线活动和会议

### 7. 网络流量特征

Discord 流量特点：
- 大量 HTTPS 连接
- WebSocket 长连接
- UDP 语音流（低延迟）
- CDN 内容下载
- 心跳包保持连接

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_DISCORD, 行 1916-1922)
- **协议检测**: `src/lib/protocols/discord.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_DISCORD:
  if (l7_protocol.proto.master_protocol != NDPI_PROTOCOL_TLS) {
    ndpi_serialize_start_of_block(serializer, "discord");
    ndpi_serialize_string_string(serializer, "client_ip", flow->protos.discord.client_ip);
    ndpi_serialize_end_of_block(serializer);
  }
  break;
```

注意：当 Discord 通过 TLS 时，不输出 discord 块，仅识别为 TLS 协议。

## 相关函数

### nDPI 检测函数

- `ndpi_search_discord()` - Discord 协议检测器

## 参考资料

- [Discord Official Website](https://discord.com/)
- [Discord Developer Portal](https://discord.com/developers/docs)
- [Discord API Documentation](https://discord.com/developers/docs/intro)
- [nDPI Discord 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/discord.c)

## 常见用例

### 1. 游戏通信

游戏玩家实时语音和文本沟通。

### 2. 社区管理

在线社区、粉丝群、兴趣小组。

### 3. 团队协作

远程团队的项目协作和沟通。

### 4. 在线教育

虚拟教室、学习小组讨论。

### 5. 客户支持

企业使用 Discord 提供客户支持服务。

### 6. 活动组织

线上活动、会议、讲座的组织和进行。

### 7. 内容创作

创作者与粉丝互动、社区建设。

## Discord Bot 开发

Discord 支持 Bot 开发：
- **自动化任务**: 消息管理、用户欢迎
- **集成服务**: 连接外部 API 和服务
- **游戏功能**: 游戏统计、排行榜
- **实用工具**: 投票、提醒、音乐播放

### Bot 开发语言

- JavaScript/TypeScript (discord.js)
- Python (discord.py)
- Java (JDA)
- Go (discordgo)
- C# (Discord.Net)

## 网络监控

### Discord 流量识别

```bash
# 使用 nDPI 检测
ndpiReader -i eth0 | grep Discord

# 检查 Discord 域名
nslookup discord.com
nslookup gateway.discord.gg
```

### 流量分析

监控指标：
- 连接频率
- 数据传输量
- 语音通话时长
- 用户活跃度

## 带宽管理

Discord 带宽使用：
- **文本消息**: 很低（< 1 Kbps）
- **语音通话**: 约 64-128 Kbps
- **视频通话**: 约 1-4 Mbps
- **屏幕共享**: 约 2-8 Mbps
- **文件上传/下载**: 根据文件大小

### QoS 配置

为 Discord 配置 QoS：
- 优先级：语音 > 视频 > 文本
- 确保低延迟
- 稳定的带宽分配

## 防火墙配置

允许 Discord 流量：

```bash
# 允许 HTTPS
iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT

# 允许 WebRTC UDP
iptables -A OUTPUT -p udp --dport 50000:65535 -j ACCEPT

# 允许 Discord 域名（使用域名过滤）
# 配置防火墙规则允许 *.discord.com, *.discord.gg
```

## 企业部署考虑

### 安全策略

- 限制文件类型
- 内容审核
- 数据保留政策
- 用户权限管理
- 外部链接控制

### 合规性

- 数据隐私（GDPR、CCPA）
- 内容归档
- 审计日志
- 员工培训

### 性能优化

- 使用区域语音服务器
- 配置适当的带宽
- 监控网络质量
- 优化 WebRTC 设置

## Discord Nitro

Discord 高级订阅：
- 更高上传限制（100 MB）
- 高清视频和屏幕共享
- 自定义表情符号
- 服务器增强
- 徽章和个人资料装饰

## 替代平台

其他通讯平台：
- **Slack**: 企业协作
- **Microsoft Teams**: 企业通讯
- **TeamSpeak**: 游戏语音
- **Mumble**: 开源语音
- **Guilded**: 游戏社区

## 故障排查

### 连接问题

- 检查网络连接
- 验证防火墙规则
- 测试 DNS 解析
- 清除缓存

### 语音质量问题

- 检查带宽
- 选择更近的服务器
- 调整语音设置
- 检查网络抖动

### 性能问题

- 更新应用程序
- 禁用硬件加速
- 关闭不必要的功能
- 减少服务器数量

## 相关协议

- [TLS.md](TLS.md) - Discord 使用 HTTPS/WSS
- [STUN.md](STUN.md) - WebRTC NAT 穿越
