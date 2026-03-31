# TFTP 协议文档

## 协议概述

- **协议全称**: Trivial File Transfer Protocol (简单文件传输协议)
- **协议 ID**: NDPI_PROTOCOL_TFTP (96)
- **协议类别**: 文件传输协议
- **传输层**: UDP (端口 69)

## 简介

TFTP 是一种简单的文件传输协议，设计用于在没有认证的情况下读取或写入文件。由于其简单性，TFTP 常用于网络启动、路由器配置传输等场景。nDPI 可以检测 TFTP 流量并提取传输的文件名。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "TFTP",
    "tftp": {
      "filename": "config.cfg"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| filename | string | 传输的文件名 | "config.cfg" |

## 实际示例

### 示例 1: 网络设备配置下载

```json
{
  "ndpi": {
    "protocol": "TFTP",
    "tftp": {
      "filename": "router-config.txt"
    }
  }
}
```

### 示例 2: PXE 网络启动

```json
{
  "ndpi": {
    "protocol": "TFTP",
    "tftp": {
      "filename": "pxelinux.0"
    }
  }
}
```

### 示例 3: 固件升级

```json
{
  "ndpi": {
    "protocol": "TFTP",
    "tftp": {
      "filename": "firmware-v2.3.1.bin"
    }
  }
}
```

### 示例 4: IP 电话配置

```json
{
  "ndpi": {
    "protocol": "TFTP",
    "tftp": {
      "filename": "SEP001122334455.cnf.xml"
    }
  }
}
```

## 注意事项

### 1. TFTP 端口

- **UDP 69**: TFTP 服务器监听端口
- **动态端口**: 数据传输使用随机端口（TID）
- 每个传输使用独立的端口

### 2. TFTP 操作类型

| 操作码 | 名称 | 说明 |
|--------|------|------|
| 1 | RRQ (Read Request) | 读文件请求 |
| 2 | WRQ (Write Request) | 写文件请求 |
| 3 | DATA | 数据包 |
| 4 | ACK | 确认包 |
| 5 | ERROR | 错误包 |

### 3. TFTP 工作流程

**读文件 (RRQ)**:
```
Client          Server
  |   RRQ(filename)  |
  |----------------->|
  |   DATA(block 1)  |
  |<-----------------|
  |   ACK(block 1)   |
  |----------------->|
  |   DATA(block 2)  |
  |<-----------------|
  |   ACK(block 2)   |
  |----------------->|
  ...
```

**写文件 (WRQ)**:
```
Client          Server
  |   WRQ(filename)  |
  |----------------->|
  |   ACK(block 0)   |
  |<-----------------|
  |   DATA(block 1)  |
  |----------------->|
  |   ACK(block 1)   |
  |<-----------------|
  ...
```

### 4. TFTP 特点

**优点**：
- 极其简单，易于实现
- 占用资源少
- 适合嵌入式系统
- 无需认证，快速部署

**缺点**：
- 无安全性（无加密、无认证）
- 无目录列表功能
- 传输效率较低
- 不适合大文件传输

### 5. 安全风险

**TFTP 的主要安全问题**：
- **无认证**: 任何人都可以读写文件
- **无加密**: 数据明文传输
- **信息泄露**: 敏感配置可能被窃取
- **恶意上传**: 可能上传恶意文件

**安全建议**：
- 仅在隔离的管理网络中使用
- 限制可访问的文件和目录
- 使用防火墙规则限制访问
- 考虑使用 SFTP 或 SCP 替代
- 监控 TFTP 活动

### 6. 传输模式

TFTP 支持三种传输模式：
- **netascii**: ASCII 模式，处理文本文件
- **octet**: 二进制模式，不做任何转换
- **mail**: 已弃用，不再使用

### 7. 数据块大小

- 默认块大小：512 字节
- 可通过选项协商更大的块大小（blksize 选项）
- 最后一个块小于 512 字节表示传输结束

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_TFTP, 行 1695-1699)
- **协议检测**: `src/lib/protocols/tftp.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_TFTP:
  ndpi_serialize_start_of_block(serializer, "tftp");
  ndpi_serialize_string_string(serializer, "filename", flow->protos.tftp.filename);
  ndpi_serialize_end_of_block(serializer);
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_tftp()` - TFTP 协议检测器

## 参考资料

- [RFC 1350 - The TFTP Protocol (Revision 2)](https://tools.ietf.org/html/rfc1350)
- [RFC 2347 - TFTP Option Extension](https://tools.ietf.org/html/rfc2347)
- [RFC 2348 - TFTP Blocksize Option](https://tools.ietf.org/html/rfc2348)
- [RFC 2349 - TFTP Timeout Interval and Transfer Size Options](https://tools.ietf.org/html/rfc2349)
- [nDPI TFTP 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/tftp.c)

## 常见用例

### 1. PXE 网络启动

无盘工作站通过 TFTP 加载启动镜像。

### 2. 网络设备配置

路由器、交换机配置文件的备份和恢复。

### 3. IP 电话

VoIP 电话从 TFTP 服务器下载配置文件。

### 4. 固件升级

网络设备从 TFTP 服务器下载固件更新。

### 5. 自动化部署

自动化系统使用 TFTP 传输配置和脚本。

### 6. 嵌入式系统

资源受限的嵌入式设备使用 TFTP 传输文件。

### 7. 安全监控

监控 TFTP 活动，检测未授权文件访问。

## TFTP 服务器

常见的 TFTP 服务器实现：

- **tftpd-hpa**: Linux 标准 TFTP 服务器
- **atftpd**: 高级 TFTP 服务器（支持多线程）
- **SolarWinds TFTP Server**: Windows TFTP 服务器
- **tftp-server**: Python TFTP 服务器
- **Cisco TFTP Server**: Cisco 设备专用

## 配置示例

### Linux tftpd-hpa 配置

```bash
# /etc/default/tftpd-hpa
TFTP_USERNAME="tftp"
TFTP_DIRECTORY="/var/lib/tftpboot"
TFTP_ADDRESS=":69"
TFTP_OPTIONS="--secure --create"
```

### 客户端使用

```bash
# 下载文件
tftp 192.168.1.1 -c get config.txt

# 上传文件
tftp 192.168.1.1 -c put firmware.bin
```

## TFTP 错误代码

| 错误码 | 说明 |
|--------|------|
| 0 | 未定义 |
| 1 | 文件未找到 |
| 2 | 访问违规 |
| 3 | 磁盘满或分配超额 |
| 4 | 非法 TFTP 操作 |
| 5 | 未知传输 ID |
| 6 | 文件已存在 |
| 7 | 没有此用户 |

## 性能优化

- 使用 blksize 选项增加块大小（如 1468 字节）
- 启用 windowsize 选项（TFTP 窗口化扩展）
- 优化网络 MTU 设置
- 考虑使用有损网络时的超时和重传

## 替代方案

对于需要安全性的场景，建议使用：

| 协议 | 特点 | 适用场景 |
|------|------|----------|
| SFTP | SSH 文件传输，安全 | 一般文件传输 |
| SCP | SSH 复制，简单安全 | 文件复制 |
| FTP/FTPS | 功能丰富，可加密 | 传统文件传输 |
| HTTP/HTTPS | Web 传输，广泛支持 | 现代文件分发 |

## 网络启动 (PXE)

TFTP 在 PXE 启动中的角色：

```
1. DHCP: 获取 IP 和 TFTP 服务器地址
2. TFTP: 下载 NBP (Network Bootstrap Program)
3. NBP: 通常是 pxelinux.0 或 UEFI bootloader
4. TFTP: 下载内核和 initramfs
5. 启动: 系统从网络启动
```

## 安全最佳实践

1. **网络隔离**: 仅在管理 VLAN 中启用
2. **访问控制**: 
   - 使用防火墙限制源 IP
   - 配置只读或只写目录
3. **文件限制**: 
   - 限制可访问的文件
   - 使用 chroot 限制目录访问
4. **监控审计**: 
   - 记录所有 TFTP 传输
   - 设置异常活动告警
5. **定期审查**: 
   - 检查 TFTP 目录内容
   - 删除过期文件

## 故障排查

### 常见问题

1. **传输超时**
   - 检查防火墙是否阻止 UDP
   - 验证服务器是否运行
   - 检查网络连接

2. **权限错误**
   - 确认文件权限
   - 检查目录写权限
   - 验证 TFTP 用户权限

3. **文件未找到**
   - 确认文件在 TFTP 根目录
   - 检查文件名大小写
   - 验证路径设置

## 相关协议

- [FTP.md](FTP.md) - 功能更丰富的文件传输协议
- [SSH.md](SSH.md) - SFTP/SCP 基于 SSH
- [HTTP.md](HTTP.md) - HTTP 文件传输
