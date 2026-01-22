# RSH 协议文档

## 协议概述

- **协议全称**: Remote Shell (远程外壳协议)
- **协议 ID**: NDPI_PROTOCOL_RSH (294)
- **协议类别**: 远程访问协议
- **传输层**: TCP (端口 514)

## 简介

RSH (Remote Shell) 是一种允许用户在远程计算机上执行命令的网络协议。它是 rlogin 和 rcp 等 r-commands 套件的一部分。RSH 以明文传输所有数据，现已被 SSH 等更安全的协议取代。nDPI 可以检测 RSH 流量并提取用户名和命令信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "RSH",
    "rsh": {
      "client_username": "alice",
      "server_username": "root",
      "command": "ls -la /home"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| client_username | string | 客户端用户名 | "alice" |
| server_username | string | 服务器用户名 | "root" |
| command | string | 执行的命令 | "ls -la /home" |

## 实际示例

### 示例 1: 远程命令执行

```json
{
  "ndpi": {
    "protocol": "RSH",
    "rsh": {
      "client_username": "admin",
      "server_username": "admin",
      "command": "df -h"
    }
  }
}
```

### 示例 2: 系统管理操作

```json
{
  "ndpi": {
    "protocol": "RSH",
    "rsh": {
      "client_username": "operator",
      "server_username": "root",
      "command": "systemctl restart nginx"
    }
  }
}
```

### 示例 3: 文件操作

```json
{
  "ndpi": {
    "protocol": "RSH",
    "rsh": {
      "client_username": "user1",
      "server_username": "user1",
      "command": "cat /etc/hosts"
    }
  }
}
```

## 注意事项

### 1. 严重的安全风险

**RSH 的主要安全问题**：
- **明文传输**: 所有数据包括密码、命令和输出都是明文
- **无加密**: 易受网络嗅探攻击
- **弱认证**: 基于主机信任，容易伪造
- **无完整性保护**: 数据可被篡改

**强烈建议**：
- **立即停用 RSH**，使用 SSH 替代
- 如必须使用，仅在隔离的受信任网络
- 禁用所有面向互联网的 RSH 服务

### 2. RSH 端口

- **TCP 514**: RSH 服务端口
- **stderr 端口**: 动态分配用于错误输出

### 3. R-Commands 套件

RSH 是 r-commands 系列的一部分：
- **rsh**: 远程执行命令
- **rlogin**: 远程登录
- **rcp**: 远程文件复制
- **rexec**: 远程执行（需要密码）

### 4. 认证机制

RSH 使用基于主机的认证：
- `.rhosts` 文件: 用户级信任列表
- `/etc/hosts.equiv`: 系统级信任列表
- 通过 IP 地址和用户名验证

### 5. RSH vs SSH

| 特性 | RSH | SSH |
|------|-----|-----|
| 加密 | 无 | 是 |
| 认证 | 基于主机 | 强认证（密钥/密码） |
| 完整性 | 无 | 是 |
| 端口转发 | 不支持 | 支持 |
| 文件传输 | rcp | SFTP/SCP |
| 安全性 | 极低 | 高 |

### 6. 工作流程

RSH 连接建立过程：
```
1. 客户端连接到服务器端口 514
2. 发送客户端用户名
3. 发送服务器用户名
4. 发送要执行的命令
5. 服务器验证信任关系
6. 执行命令并返回输出
```

### 7. 字段可用性

- `client_username`: 发起命令的用户
- `server_username`: 在服务器上以此用户身份执行
- `command`: 要执行的具体命令
- 所有字段从 RSH 协议握手中提取

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_RSH, 行 1672-1678)
- **协议检测**: `src/lib/protocols/rsh.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_RSH:
  ndpi_serialize_start_of_block(serializer, "rsh");
  ndpi_serialize_string_string(serializer, "client_username", flow->protos.rsh.client_username);
  ndpi_serialize_string_string(serializer, "server_username", flow->protos.rsh.server_username);
  ndpi_serialize_string_string(serializer, "command", flow->protos.rsh.command);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_rsh()` - RSH 协议检测器

## 参考资料

- [RFC 1282 - BSD Rlogin](https://tools.ietf.org/html/rfc1282) (相关协议)
- [Linux rsh Manual Page](https://linux.die.net/man/1/rsh)
- [nDPI RSH 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/rsh.c)

## 常见用例

### 1. 遗留系统（历史用途）

Unix 系统间的远程命令执行（已过时）。

### 2. 自动化脚本（不推荐）

旧的自动化脚本可能仍在使用 RSH（应迁移）。

### 3. 集群管理（历史）

早期 HPC 集群管理（现代集群使用 SSH）。

### 4. 安全审计

检测网络中的 RSH 使用，要求迁移到 SSH。

### 5. 威胁检测

监控 RSH 活动，识别潜在的攻击行为。

## 安全威胁

### 1. 凭据窃取

网络嗅探器可轻易捕获所有认证信息和命令。

### 2. 命令注入

攻击者可能拦截和修改命令。

### 3. 中间人攻击

无法验证服务器身份，易受 MITM 攻击。

### 4. IP 欺骗

基于 IP 的认证容易被欺骗。

### 5. 会话劫持

攻击者可接管活动会话。

## 替代方案

### SSH (强烈推荐)

```bash
# 替代 rsh
ssh user@hostname command

# 替代 rcp
scp file user@hostname:/path/
sftp user@hostname

# SSH 的优势
- 强加密
- 密钥认证
- 端口转发
- 会话完整性保护
```

### 其他安全替代

- **Ansible**: 自动化和配置管理（基于 SSH）
- **Fabric**: Python 远程执行库（基于 SSH）
- **Clustershell**: 集群管理工具（基于 SSH）

## 迁移指南

### 从 RSH 迁移到 SSH

1. **安装 SSH 服务器和客户端**
```bash
# Debian/Ubuntu
apt-get install openssh-server openssh-client

# RHEL/CentOS
yum install openssh-server openssh-clients
```

2. **生成 SSH 密钥对**
```bash
ssh-keygen -t rsa -b 4096
ssh-copy-id user@hostname
```

3. **更新脚本**
```bash
# 旧的 RSH 命令
rsh hostname command

# 新的 SSH 命令
ssh hostname command
```

4. **禁用 RSH 服务**
```bash
systemctl stop rsh.socket
systemctl disable rsh.socket
```

## 检测和缓解

### 检测 RSH 使用

```bash
# 使用 nDPI 检测
ndpiReader -i eth0 | grep RSH

# 使用 tcpdump
tcpdump -i eth0 port 514

# 使用 netstat
netstat -an | grep :514
```

### 缓解措施

1. **禁用 RSH 服务**
```bash
# systemd
systemctl stop rsh.socket
systemctl disable rsh.socket
systemctl mask rsh.socket

# xinetd
chkconfig rsh off
```

2. **防火墙规则**
```bash
# iptables
iptables -A INPUT -p tcp --dport 514 -j DROP

# firewalld  
firewall-cmd --remove-service=rsh --permanent
```

3. **网络监控**
- 使用 IDS/IPS 检测 RSH 流量
- 设置告警
- 定期审计网络流量

## 历史和现状

- **1980s**: RSH 开发，广泛使用
- **1990s**: 安全问题凸显
- **1995**: SSH 发布，提供安全替代
- **2000s**: RSH 逐渐被废弃
- **现在**: 仅存在于遗留系统，严重不推荐

## 安全最佳实践

1. **立即禁用**: 在所有系统上禁用 RSH
2. **迁移到 SSH**: 使用 SSH 替代所有 RSH 功能
3. **网络隔离**: 如必须使用，仅在隔离网络
4. **监控审计**: 监控所有 RSH 活动
5. **定期扫描**: 扫描网络中的 RSH 服务
6. **员工培训**: 教育团队成员使用安全协议

## .rhosts 文件风险

`.rhosts` 文件允许无密码访问：

```bash
# ~/.rhosts 内容示例
hostname username
```

**风险**：
- 易被滥用
- 难以管理
- 无审计记录
- 应立即删除所有 `.rhosts` 文件

## 相关协议

- [SSH.md](SSH.md) - 安全的远程访问替代方案
- [Telnet.md](Telnet.md) - 另一个不安全的远程访问协议
