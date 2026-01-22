# BitTorrent 协议文档

## 协议概述

- **协议全称**: BitTorrent Peer-to-Peer Protocol (BitTorrent 点对点协议)
- **协议 ID**: NDPI_PROTOCOL_BITTORRENT (37)
- **协议类别**: 点对点文件共享协议
- **传输层**: TCP/UDP (多种端口)

## 简介

BitTorrent 是一种点对点 (P2P) 文件共享协议，通过将文件分割成小块并在多个对等节点之间分发来实现高效的文件传输。nDPI 可以检测 BitTorrent 流量并提取 info hash 信息。

## JSON 输出格式

### 完整结构

```json
{
  "ndpi": {
    "protocol": "BitTorrent",
    "bittorrent": {
      "hash": "2c6b6858d61da9543d4231a71db4b1c9264b0685"
    }
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 | 示例值 |
|--------|------|------|--------|
| hash | string | BitTorrent info hash (SHA-1) | "2c6b6858d61da9543d4231a71db4b1c9264b0685" |

### Info Hash

Info hash 是 torrent 文件的唯一标识符：
- 使用 SHA-1 算法计算
- 40 个十六进制字符
- 用于识别正在传输的特定文件或文件集
- 可用于追踪特定的 torrent 内容

## 实际示例

### 示例 1: 标准 BitTorrent 连接

```json
{
  "ndpi": {
    "protocol": "BitTorrent",
    "bittorrent": {
      "hash": "2c6b6858d61da9543d4231a71db4b1c9264b0685"
    }
  }
}
```

### 示例 2: Linux 发行版下载

```json
{
  "ndpi": {
    "protocol": "BitTorrent",
    "bittorrent": {
      "hash": "a1b2c3d4e5f6789012345678901234567890abcd"
    }
  }
}
```

### 示例 3: 空 Hash（无法提取）

```json
{
  "ndpi": {
    "protocol": "BitTorrent",
    "bittorrent": {
      "hash": ""
    }
  }
}
```

## 注意事项

### 1. BitTorrent 工作原理

BitTorrent 文件共享流程：
1. 用户获取 `.torrent` 文件或 magnet 链接
2. BitTorrent 客户端连接到 tracker 或 DHT
3. 获取拥有该文件的 peer 列表
4. 从多个 peer 下载文件块
5. 同时向其他 peer 上传已下载的块

### 2. BitTorrent 组件

- **Peer**: 参与文件共享的节点（既下载也上传）
- **Tracker**: 追踪 peer 列表的服务器
- **DHT (Distributed Hash Table)**: 去中心化的 peer 发现机制
- **Seed**: 拥有完整文件的 peer（只上传）
- **Leech**: 正在下载的 peer

### 3. BitTorrent 协议类型

**传统 BitTorrent**：
- 基于 TCP
- 需要 tracker 服务器

**DHT (Distributed Hash Table)**：
- 去中心化
- 不需要 tracker
- 使用 UDP

**μTP (Micro Transport Protocol)**：
- UDP 传输协议
- 更友好的拥塞控制
- 不影响其他网络流量

### 4. 端口使用

BitTorrent 没有固定端口：
- 客户端使用随机端口
- 常见默认端口：6881-6889
- 现代客户端使用随机高位端口
- 可配置端口转发

### 5. 加密

许多 BitTorrent 客户端支持协议加密：
- **PE/MSE (Protocol Encryption/Message Stream Encryption)**
- 防止 ISP 节流
- 不是为了隐私，主要是反检测
- nDPI 仍可检测加密的 BitTorrent 流量

### 6. Hash 提取

- Hash 从 BitTorrent 握手消息中提取
- 如果连接加密或握手未完成，hash 可能为空
- Hash 可用于识别具体传输的内容

### 7. 合法用途

BitTorrent 协议本身是中性的：
- Linux 发行版分发
- 开源软件发布
- 大文件的企业内部分发
- 公共领域内容共享

## 源代码位置

- **实现文件**: `src/lib/ndpi_utils.c` (case NDPI_PROTOCOL_BITTORRENT, 行 1569-1589)
- **协议检测**: `src/lib/protocols/bittorrent.c`

### 关键代码片段

```c
case NDPI_PROTOCOL_BITTORRENT:
  {
    u_int i, j, n = 0;
    char bittorent_hash[sizeof(flow->protos.bittorrent.hash)*2+1];

    for(i=0, j = 0; j < sizeof(bittorent_hash)-1; i++) {
      snprintf(&bittorent_hash[j],
               sizeof(bittorent_hash) - j,
               "%02x",
               flow->protos.bittorrent.hash[i]);
      j += 2, n += flow->protos.bittorrent.hash[i];
    }

    if(n == 0) bittorent_hash[0] = '\0';

    ndpi_serialize_start_of_block(serializer, "bittorrent");
    ndpi_serialize_string_string(serializer, "hash", bittorent_hash);
    ndpi_serialize_end_of_block(serializer);
  }
  break;
```

## 相关函数

### nDPI 检测函数

- `ndpi_search_bittorrent()` - BitTorrent 协议检测器

## 参考资料

- [BitTorrent Protocol Specification](http://www.bittorrent.org/beps/bep_0003.html)
- [BEP 3 - The BitTorrent Protocol Specification](http://www.bittorrent.org/beps/bep_0003.html)
- [BEP 5 - DHT Protocol](http://www.bittorrent.org/beps/bep_0005.html)
- [BEP 9 - Extension for Peers to Send Metadata Files](http://www.bittorrent.org/beps/bep_0009.html)
- [BEP 29 - μTorrent transport protocol](http://www.bittorrent.org/beps/bep_0029.html)
- [nDPI BitTorrent 协议检测源码](https://github.com/ntop/nDPI/blob/dev/src/lib/protocols/bittorrent.c)

## 常见用例

### 1. 软件分发

Linux 发行版、开源软件使用 BitTorrent 分发。

### 2. 内容分发网络 (CDN)

某些 CDN 使用 P2P 技术减轻服务器负载。

### 3. 游戏更新

游戏厂商（如 Blizzard）使用 P2P 分发游戏更新。

### 4. 科学数据共享

大型科学数据集通过 BitTorrent 分发。

### 5. 网络监控

监控 BitTorrent 流量识别带宽使用模式。

### 6. 流量管理

ISP 和企业网络管理 BitTorrent 流量。

### 7. 内容追踪

使用 info hash 追踪特定内容的传播。

## BitTorrent 客户端

常见的 BitTorrent 客户端：
- **qBittorrent** - 开源，跨平台
- **Transmission** - 轻量级，Linux/Mac
- **Deluge** - 功能丰富
- **μTorrent** - Windows 流行客户端
- **BitTorrent** - 官方客户端
- **rTorrent** - 命令行客户端

## 网络影响

BitTorrent 流量特征：
- 占用大量带宽
- 大量 TCP 连接
- 持续的上传和下载
- 可能影响网络性能

## 流量管理策略

网络管理员可采取的措施：
1. **QoS (Quality of Service)** - 限制 BitTorrent 优先级
2. **带宽限制** - 为 P2P 流量设置带宽上限
3. **时间限制** - 非工作时间允许 P2P
4. **协议分析** - 使用 DPI 识别 BitTorrent
5. **端口限制** - 阻止常用 BitTorrent 端口（效果有限）

## 检测技术

nDPI 检测 BitTorrent 的方法：
- 握手消息特征识别
- DHT 流量模式
- 端口行为分析
- 加密流量启发式检测
- Info hash 提取

## 隐私考虑

- BitTorrent 传输通常是公开的
- Tracker 可以看到 IP 地址
- DHT 查询暴露正在下载的内容
- 建议使用 VPN 保护隐私（如需要）

## 合法性

- BitTorrent 协议本身是合法的
- 用途决定合法性
- 企业和组织应制定明确的 P2P 使用政策
