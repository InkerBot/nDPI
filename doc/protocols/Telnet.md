# Telnet 协议文档

## 协议概述

- **协议全称**: Telecommunications Network Protocol (电信网络协议)
- **协议 ID**: NDPI_PROTOCOL_TELNET (77)
- **协议类别**: 远程访问协议
- **传输层**: TCP (端口 23)

## 简介

Telnet 是一种用于远程登录和命令行访问的网络协议。它提供了基于文本的双向交互通信。由于 Telnet 以明文传输所有数据（包括密码），现已被 SSH 等更安全的协议取代，但在某些遗留系统和网络设备中仍在使用。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "Telnet",
    "telnet": {
      "username": "admin",
      "password": "password123"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| username | string | Telnet 登录用户名 | "admin" |
| password | string | Telnet 登录密码 | "password123" |

## 实际示例

### 示例 1: 路由器 Telnet 登录

```json
{
  "ndpi": {
    "protocol": "Telnet",
    "telnet": {
      "username": "cisco",
      "password": "cisco"
    }
  }
}
```

### 示例 2: 服务器 Telnet 访问

```json
{
  "ndpi": {
    "protocol": "Telnet",
    "telnet": {
      "username": "root",
      "password": "toor123"
    }
  }
}
```

### 示例 3: 仅用户名

```json
{
  "ndpi": {
    "protocol": "Telnet",
    "telnet": {
      "username": "operator"
    }
  }
}
```

## 注意事项

### 1. 严重的安全风险

**Telnet 的主要安全问题**：
- **明文传输** - 所有数据包括密码都是明文
- **无加密** - 任何网络监听者都可以看到内容
- **易受攻击** - 中间人攻击、会话劫持等
- **无身份验证** - 服务器身份无法验证

**强烈建议**：
- 在任何情况下都应使用 SSH 替代 Telnet
- 如必须使用，仅在隔离的受信任网络中
- 立即禁用面向互联网的 Telnet 服务

### 2. Telnet 端口

| 端口 | 用途 |
|------|------|
| 23 | Telnet 默认端口 |
| 其他 | 某些设备可能使用非标准端口 |

### 3. Telnet 命令和选项

Telnet 协议使用 IAC (Interpret As Command) 字节 (0xFF) 进行协商：

常见 Telnet 选项：
- **ECHO** - 回显控制
- **SUPPRESS-GO-AHEAD** - 抑制 GA 信号
- **STATUS** - 状态查询
- **TIMING-MARK** - 时间标记
- **TERMINAL-TYPE** - 终端类型
- **WINDOW-SIZE** - 窗口大小
- **TERMINAL-SPEED** - 终端速度
- **REMOTE-FLOW-CONTROL** - 远程流控制
- **LINEMODE** - 行模式
- **ENVIRONMENT** - 环境变量

### 4. Telnet 与 SSH 对比

| 特性 | Telnet | SSH |
|------|--------|-----|
| 加密 | 无 | 是 |
| 认证 | 基本 | 强认证（密钥/密码） |
| 完整性 | 无 | 是 |
| 端口转发 | 不支持 | 支持 |
| 文件传输 | 不支持 | 支持 (SFTP/SCP) |
| 安全性 | 极低 | 高 |

### 5. 遗留系统

Telnet 仍在使用的场景：
- 老旧的网络设备（路由器、交换机）
- 工业控制系统
- 串口服务器
- 某些嵌入式设备
- BBS 系统

### 6. Telnet 作为测试工具

Telnet 仍可用作调试工具：
```bash
telnet smtp.example.com 25    # 测试 SMTP
telnet pop3.example.com 110   # 测试 POP3
telnet example.com 80         # 测试 HTTP
```

注意：这只是测试连接性，不应用于生产访问。

### 7. 字段可用性

- `username` 和 `password` 从 Telnet 会话中提取
- 某些 Telnet 会话可能不使用标准认证
- 如果无法提取，字段可能为空

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_TELNET, 行 1688-1693)
- **协议检测**: `src/lib/protocols/telnet.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_TELNET:
  ndpi_serialize_start_of_block(serializer, "telnet");
  ndpi_serialize_string_string(serializer, "username", flow->protos.telnet.username);
  ndpi_serialize_string_string(serializer, "password", flow->protos.telnet.password);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_telnet()` - Telnet 协议检测器

## 参考资料

- [RFC 854 - Telnet Protocol Specification](https://tools.ietf.org/html/rfc854)
- [RFC 855 - Telnet Option Specifications](https://tools.ietf.org/html/rfc855)
- [RFC 1091 - Telnet Terminal-Type Option](https://tools.ietf.org/html/rfc1091)
- [RFC 1073 - Telnet Window Size Option](https://tools.ietf.org/html/rfc1073)
- [nDPI Telnet 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/telnet.c)

## 常见用例

### 1. 网络设备管理（遗留）

老旧的路由器和交换机管理。

### 2. 串口服务器访问

通过网络访问串口设备。

### 3. BBS 系统

Bulletin Board Systems (电子公告板)。

### 4. 工业系统

某些工业控制系统的访问接口。

### 5. 测试和调试

测试服务端口的连接性。

### 6. 安全审计

检测网络中的 Telnet 使用，要求迁移到 SSH。

### 7. 威胁检测

监控 Telnet 登录尝试，检测暴力破解攻击。

## 安全威胁

### 1. 凭据窃取

- 网络嗅探器可以轻易捕获用户名和密码
- 所有命令和输出都可见

### 2. 中间人攻击

- 攻击者可以拦截和修改通信
- 无法验证服务器身份

### 3. 会话劫持

- 攻击者可以接管活动会话
- 无会话完整性保护

### 4. 重放攻击

- 捕获的认证信息可被重放使用

## 替代方案

### SSH (推荐)

```bash
# 替代 Telnet
ssh username@hostname

# SSH 的优势
- 加密通信
- 强认证
- 密钥认证
- 端口转发
- 文件传输 (SCP/SFTP)
```

### 其他安全替代

- **RDP** - Windows 远程桌面（有安全选项）
- **VNC over SSH** - 图形界面远程访问
- **Web 管理界面** (HTTPS) - 基于 Web 的管理
- **Console Server** - 带外管理

## 安全最佳实践

1. **禁用 Telnet**
   - 在所有系统上禁用 Telnet 服务
   - 使用 SSH 替代

2. **网络隔离**
   - 如必须使用，仅在隔离的管理网络
   - 使用防火墙严格限制访问

3. **监控和审计**
   - 监控 Telnet 连接尝试
   - 记录所有 Telnet 活动
   - 设置告警

4. **迁移计划**
   - 制定从 Telnet 迁移到 SSH 的计划
   - 升级支持 SSH 的设备
   - 培训人员使用 SSH

5. **临时措施**
   - 如暂时无法禁用：
     - VPN 隧道保护
     - 使用强密码
     - 限制源 IP
     - 最小权限账户

## 检测和缓解

### 检测 Telnet 使用

```bash
# 使用 nDPI 检测
ndpiReader -i eth0 | grep Telnet

# 使用 tcpdump
tcpdump -i eth0 port 23

# 使用 netstat
netstat -an | grep :23
```

### 缓解措施

1. 使用 IDS/IPS 规则阻止 Telnet
2. 防火墙规则阻止端口 23
3. 网络访问控制列表 (ACL)
4. 定期扫描和审计

## 历史和现状

- **1969** - Telnet 开发
- **1983** - RFC 854 标准化
- **1990s** - 广泛使用的远程访问协议
- **2000s** - SSH 逐渐取代 Telnet
- **现在** - 仅遗留系统使用，被认为不安全

## 相关协议

- [SSH.md](SSH.md) - 安全替代方案
- [FTP.md](FTP.md) - 另一个不安全的协议
