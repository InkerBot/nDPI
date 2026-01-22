# 未文档化的 nDPI 协议列表

## 概述

本文档列出了 nDPI 协议库中所有**尚未创建详细 JSON 输出文档**的协议。

### 统计信息

- **nDPI 总协议数**: 469
- **已文档化协议**: 30
- **未文档化协议**: 437

### 说明

以下协议被 nDPI 检测和识别，但在 `ndpi_dpi2json` 函数中**没有专门的 JSON 序列化块**。这意味着这些协议在 JSON 输出中只会显示协议名称，不会包含协议特定的详细字段。

已文档化的 30 个协议包含完整的 JSON 输出格式说明、字段详解和实际示例。

---

## 未文档化协议列表 (437)

### 流媒体和视频服务 (59)

| 协议名 | 说明 |
|--------|------|
| NETFLIX | Netflix 流媒体服务 |
| YOUTUBE | YouTube 视频服务 |
| YOUTUBE_UPLOAD | YouTube 上传服务 |
| AMAZON_VIDEO | Amazon Prime Video |
| DISNEYPLUS | Disney+ 流媒体 |
| HULU | Hulu 流媒体服务 |
| HBO | HBO 流媒体服务 |
| TWITCH | Twitch 直播平台 |
| TIKTOK | TikTok 短视频 |
| DAZN | DAZN 体育流媒体 |
| ESPN | ESPN 体育流媒体 |
| IQIYI | 爱奇艺视频 |
| TENCENTVIDEO | 腾讯视频 |
| APPLETVPLUS | Apple TV+ |
| PARAMOUNTPLUS | Paramount+ |
| PEACOCK | Peacock 流媒体 |
| SHOWTIME | Showtime |
| VUDU | Vudu 视频服务 |
| IFLIX | iFlix 流媒体 |
| VIMEO | Vimeo 视频平台 |
| DAILYMOTION | Dailymotion 视频 |
| SPOTIFY | Spotify 音乐流媒体 |
| SOUNDCLOUD | SoundCloud 音频平台 |
| PANDORA | Pandora 音乐 |
| DEEZER | Deezer 音乐流媒体 |
| TIDAL | Tidal 高品质音乐 |
| TUNEIN | TuneIn 网络电台 |
| IHEARTRADIO | iHeartRadio |
| SIRIUSXMRADIO | SiriusXM 卫星广播 |
| LASTFM | Last.fm 音乐 |
| LIVESTREAM | 直播流媒体 |
| ICECAST | Icecast 流媒体服务器 |
| RTMP | Real-Time Messaging Protocol |
| HLS | HTTP Live Streaming |
| MPEGDASH | MPEG-DASH 自适应流 |
| MPEGTS | MPEG Transport Stream |
| RTP | Real-time Transport Protocol |
| RTCP | RTP Control Protocol |
| RTSP | Real Time Streaming Protocol |
| RTPS | Real-Time Publish Subscribe |
| SRTP | Secure RTP |
| MEGACO | Media Gateway Control |
| MGCP | Media Gateway Control Protocol |
| H323 | H.323 多媒体通信 |
| IAX | Inter-Asterisk eXchange |
| SKINNY | Cisco SCCP |
| DIRECTV | DirecTV 服务 |
| RUMBLE | Rumble 视频平台 |
| RUTUBE | RuTube 俄罗斯视频 |
| KICK | Kick 直播平台 |
| BLUESKY | Bluesky 社交视频 |
| CNN | CNN 新闻流媒体 |
| VIVOX | Vivox 语音聊天 |
| SONOS | Sonos 音频系统 |
| OOKLA | Ookla 速度测试 |
| BLOOMBERG | Bloomberg 财经流媒体 |
| FASTLY | Fastly CDN |
| AKAMAI | Akamai CDN |

### 社交媒体和通讯 (47)

| 协议名 | 说明 |
|--------|------|
| FACEBOOK | Facebook 社交网络 |
| FACEBOOK_MESSENGER | Facebook Messenger |
| FACEBOOK_REEL_STORY | Facebook Reels/Stories |
| FACEBOOK_VOIP | Facebook 语音通话 |
| WHATSAPP | WhatsApp 即时通讯 |
| WHATSAPP_CALL | WhatsApp 语音/视频通话 |
| WHATSAPP_FILES | WhatsApp 文件传输 |
| INSTAGRAM | Instagram 图片社交 |
| TWITTER | Twitter/X 社交平台 |
| THREADS | Threads 社交应用 |
| WECHAT | 微信 |
| QQ | QQ 即时通讯 |
| OICQ | OICQ (早期 QQ) |
| TELEGRAM | Telegram 即时通讯 |
| TELEGRAM_VOIP | Telegram 语音通话 |
| SIGNAL | Signal 加密通讯 |
| SIGNAL_VOIP | Signal 语音通话 |
| VIBER | Viber 通讯 |
| VIBER_VOIP | Viber 语音通话 |
| LINE | LINE 即时通讯 |
| LINE_CALL | LINE 语音通话 |
| KAKAOTALK | KakaoTalk 即时通讯 |
| KAKAOTALK_VOICE | KakaoTalk 语音 |
| SNAPCHAT | Snapchat |
| SNAPCHAT_CALL | Snapchat 通话 |
| VK | VKontakte 俄罗斯社交 |
| LINKEDIN | LinkedIn 职业社交 |
| REDDIT | Reddit 论坛 |
| PINTEREST | Pinterest 图片分享 |
| TUMBLR | Tumblr 博客平台 |
| SLACK | Slack 团队协作 |
| TEAMSPEAK | TeamSpeak 语音 |
| MUMBLE | Mumble 语音聊天 |
| JABBER | XMPP/Jabber |
| IRC | Internet Relay Chat |
| SINA_WEIBO | 新浪微博 |
| SINA | 新浪网 |
| DINGTALK | 钉钉 |
| IMO | imo 视频通话 |
| THREEMA | Threema 加密通讯 |
| MASTODON | Mastodon 去中心化社交 |
| BADOO | Badoo 社交约会 |
| PALTALK | Paltalk 视频聊天 |
| TRUPHONE | Truphone 通讯 |
| FUZE | Fuze 企业通讯 |
| GOTO | GoTo 协作平台 |
| WEBEX | Cisco Webex |

### 游戏和娱乐 (42)

| 协议名 | 说明 |
|--------|------|
| STEAM | Steam 游戏平台 |
| EPICGAMES | Epic Games Store |
| RIOTGAMES | Riot Games (英雄联盟等) |
| BLIZZARD | 暴雪游戏平台 |
| ELECTRONICARTS | EA Games |
| ACTIVISION | 动视游戏 |
| ROCKSTAR_GAMES | Rockstar Games (GTA等) |
| WORLDOFWARCRAFT | 魔兽世界 |
| DOTA2 | Dota 2 |
| CROSSFIRE | 穿越火线 |
| GUILDWARS2 | 激战2 |
| HOTS | 风暴英雄 |
| TESO | 上古卷轴OL |
| PATHOFEXILE | 流放之路 |
| ARMAGETRON | Armagetron 游戏 |
| COD_MOBILE | 使命召唤手游 |
| GENSHIN_IMPACT | 原神 |
| LOLWILDRIFT | 英雄联盟手游 |
| AMONG_US | Among Us |
| DOFUS | Dofus MMORPG |
| ROBLOX | Roblox 游戏平台 |
| XBOX | Xbox Live |
| PLAYSTATION | PlayStation Network |
| NINTENDO | Nintendo 在线服务 |
| VALVE_SDR | Valve 软件定义路由 |
| SOURCE_ENGINE | Source 引擎游戏 |
| RAKNET | RakNet 游戏网络 |
| KCP | KCP 游戏协议 |
| GAIJIN | Gaijin 游戏 (战争雷霆) |
| NEXON | Nexon 游戏 |
| NETEASE_GAMES | 网易游戏 |
| TENCENTGAMES | 腾讯游戏 |
| GEFORCENOW | GeForce NOW 云游戏 |
| BOOSTEROID | Boosteroid 云游戏 |
| BLACKNUT | Blacknut 云游戏 |
| GEARUP_BOOSTER | GearUP 游戏加速器 |
| LAGOFAST | LagoFast 游戏加速器 |
| MUDFISH | Mudfish 游戏加速器 |
| I3D | i3D.net 游戏网络 |
| CRYNET | CryEngine 网络 |
| KISMET | Kismet 游戏引擎 |
| TOCA_BOCA | Toca Boca 儿童游戏 |

### 云服务和 CDN (52)

| 协议名 | 说明 |
|--------|------|
| AMAZON | Amazon 服务 |
| AMAZON_AWS | AWS 云服务 |
| AMAZON_ALEXA | Amazon Alexa |
| AWS_API_GATEWAY | AWS API Gateway |
| AWS_CLOUDFRONT | AWS CloudFront CDN |
| AWS_COGNITO | AWS Cognito |
| AWS_DYNAMODB | AWS DynamoDB |
| AWS_EC2 | AWS EC2 |
| AWS_EMR | AWS EMR |
| AWS_KINESIS | AWS Kinesis |
| AWS_S3 | AWS S3 存储 |
| GOOGLE | Google 服务 |
| GOOGLE_CLOUD | Google Cloud |
| GOOGLE_SERVICES | Google 服务套件 |
| GOOGLE_DRIVE | Google Drive |
| GOOGLE_DOCS | Google Docs |
| GOOGLE_MAPS | Google Maps |
| GOOGLE_MEET | Google Meet |
| GOOGLE_CALL | Google 语音通话 |
| GOOGLE_CHAT | Google Chat |
| GOOGLE_CLASSROOM | Google Classroom |
| MICROSOFT | Microsoft 服务 |
| MICROSOFT_365 | Microsoft 365 |
| MICROSOFT_AZURE | Azure 云服务 |
| MS_ONE_DRIVE | OneDrive |
| MS_OUTLOOK | Outlook |
| APPLE | Apple 服务 |
| APPLE_ICLOUD | iCloud |
| APPLE_ITUNES | iTunes |
| APPLE_PUSH | Apple Push Notifications |
| APPLE_SIRI | Siri |
| APPLESTORE | App Store |
| ICLOUD_PRIVATE_RELAY | iCloud Private Relay |
| CLOUDFLARE | Cloudflare 服务 |
| CLOUDFLARE_WARP | Cloudflare WARP VPN |
| ALIBABA | 阿里巴巴 |
| ALICLOUD | 阿里云 |
| TAOBAO | 淘宝 |
| TENCENT | 腾讯服务 |
| HUAWEI | 华为服务 |
| HUAWEI_CLOUD | 华为云 |
| YANDEX | Yandex 服务 |
| YANDEX_CLOUD | Yandex Cloud |
| YANDEX_ALICE | Yandex Alice AI |
| YANDEX_DIRECT | Yandex Direct 广告 |
| YANDEX_DISK | Yandex Disk 存储 |
| YANDEX_MAIL | Yandex Mail |
| YANDEX_MARKET | Yandex Market |
| YANDEX_METRIKA | Yandex Metrika 分析 |
| YANDEX_MUSIC | Yandex Music |
| DIGITALOCEAN | DigitalOcean 云服务 |
| ORACLE | Oracle Cloud |

### VPN 和代理服务 (24)

| 协议名 | 说明 |
|--------|------|
| OPENVPN | OpenVPN |
| WIREGUARD | WireGuard VPN |
| IPSEC | IPSec VPN |
| PPTP | PPTP VPN |
| CISCOVPN | Cisco VPN |
| NORDVPN | NordVPN |
| SURFSHARK | Surfshark VPN |
| PIA | Private Internet Access |
| PROTONVPN | ProtonVPN |
| MULLVAD | Mullvad VPN |
| HOTSPOT_SHIELD | Hotspot Shield |
| TUNNELBEAR | TunnelBear VPN |
| WINDSCRIBE | Windscribe VPN |
| OPERA_VPN | Opera VPN |
| CACTUSVPN | CactusVPN |
| AVAST_SECUREDNS | Avast SecureDNS |
| TOR | Tor 匿名网络 |
| PSIPHON | Psiphon 翻墙工具 |
| ULTRASURF | UltraSurf 代理 |
| HAMACHI | LogMeIn Hamachi |
| TINC | Tinc VPN |
| TEREDO | Teredo IPv6 隧道 |
| TAILSCALE | Tailscale VPN |
| FORTICLIENT | FortiClient VPN |

### 数据库和存储 (12)

| 协议名 | 说明 |
|--------|------|
| MYSQL | MySQL 数据库 |
| POSTGRES | PostgreSQL |
| MSSQL_TDS | Microsoft SQL Server |
| ORACLE | Oracle 数据库 |
| REDIS | Redis (通过 RESP) |
| MEMCACHED | Memcached |
| CASSANDRA | Cassandra |
| ELASTICSEARCH | Elasticsearch |
| CLICKHOUSE | ClickHouse |
| CEPH | Ceph 分布式存储 |
| NFS | Network File System |
| LUSTRE | Lustre 文件系统 |

### 工业控制系统 (ICS/SCADA) (29)

| 协议名 | 说明 |
|--------|------|
| MODBUS | Modbus 工控协议 |
| S7COMM | Siemens S7 通信 |
| S7COMM_PLUS | Siemens S7 Plus |
| PROFINET_IO | PROFINET IO |
| ETHERNET_IP | EtherNet/IP |
| ETHERSBUS | EtherCAT |
| ETHERSIO | EtherNet/IP IO |
| CIP | Common Industrial Protocol |
| BACNET | BACnet 楼宇自动化 |
| DNP3 | DNP3 电力协议 |
| IEC60870 | IEC 60870-5-104 |
| IEC62056 | IEC 62056 (DLMS/COSEM) |
| IEEE_C37118 | IEEE C37.118 同步相量 |
| FINS | OMRON FINS |
| MELSEC | 三菱 MELSEC |
| BECKHOFF_ADS | Beckhoff ADS |
| HART_IP | HART-IP |
| TRISTATION | Triconex TriStation |
| OPC_UA | OPC UA |
| ISO9506_1_MMS | ISO 9506-1 MMS |
| SOMEIP | SOME/IP 汽车协议 |
| C1222 | ANSI C12.22 智能电网 |
| DLEP | Dynamic Link Exchange |
| CNP_IP | CNP over IP |
| TRDP | TRDP 列车通信 |
| NOE | NOE Protocol |
| KNXNET_IP | KNXnet/IP 智能家居 |
| MATTER | Matter 智能家居 |
| TUYA_LP | Tuya 本地协议 |

### 邮件和文件传输 (11)

| 协议名 | 说明 |
|--------|------|
| MAIL_IMAPS | IMAP over SSL |
| MAIL_POPS | POP3 over SSL |
| MAIL_SMTPS | SMTP over SSL |
| FTPS | FTP over SSL |
| FTP_DATA | FTP 数据传输 |
| RSYNC | Rsync 同步工具 |
| UFTP | UDP-based FTP |
| WEBDAV | WebDAV |
| AFP | Apple Filing Protocol |
| SMB | SMB 协议 |
| SMBV1 | SMB 版本 1 |
| SMBV23 | SMB 版本 2/3 |

### 监控和管理 (18)

| 协议名 | 说明 |
|--------|------|
| SYSLOG | Syslog 日志 |
| SFLOW | sFlow 流量采样 |
| NETFLOW | NetFlow |
| RADIUS | RADIUS 认证 |
| LDAP | LDAP 目录服务 |
| DIAMETER | Diameter 协议 |
| ZABBIX | Zabbix 监控 |
| MUNIN | Munin 监控 |
| CHECKMK | Check_MK 监控 |
| NTOP | ntop 流量监控 |
| RIPE_ATLAS | RIPE Atlas 测量 |
| NEST_LOG_SINK | Nest 日志接收 |
| CRASHLYSTICS | Crashlytics 崩溃报告 |
| MERAKI_CLOUD | Cisco Meraki Cloud |
| HAPROXY | HAProxy 协议 |
| GRAFANA | Grafana (via HTTP) |
| PROMETHEUS | Prometheus (via HTTP) |
| TELEGRAF | Telegraf (via COLLECTD) |

### 消息队列和 RPC (18)

| 协议名 | 说明 |
|--------|------|
| MQTT | MQTT 物联网协议 |
| AMQP | AMQP 消息队列 |
| APACHE_KAFKA | Apache Kafka |
| APACHE_THRIFT | Apache Thrift RPC |
| NATS | NATS 消息系统 |
| ZMQ | ZeroMQ |
| STOMP | STOMP 消息协议 |
| OPENWIRE | ActiveMQ OpenWire |
| SMPP | SMPP 短信协议 |
| JSON_RPC | JSON-RPC |
| GRPC | gRPC (via HTTP2) |
| CORBA | CORBA |
| JRMI | Java RMI |
| DCERPC | DCE/RPC |
| MS_RPCH | Microsoft RPC over HTTP |
| PROTOBUF | Protocol Buffers |
| MSGPACK | MessagePack |
| RAFT | Raft 共识协议 |

### 网络协议和基础设施 (45)

| 协议名 | 说明 |
|--------|------|
| BGP | Border Gateway Protocol |
| OSPF | OSPF 路由协议 |
| LDP | Label Distribution Protocol |
| BFD | Bidirectional Forwarding Detection |
| VRRP | Virtual Router Redundancy |
| HSRP | Hot Standby Router Protocol |
| GLBP | Gateway Load Balancing |
| HPVIRTGRP | HP Virtual Router |
| GTP | GPRS Tunneling Protocol |
| GTP_C | GTP Control Plane |
| GTP_U | GTP User Plane |
| GTP_PRIME | GTP Prime |
| PFCP | Packet Forwarding Control |
| LISP | Locator/ID Separation |
| VXLAN | Virtual Extensible LAN |
| CAPWAP | Control and Provisioning WAP |
| OPENFLOW | OpenFlow SDN |
| WHOIS_DAS | WHOIS/DAS 查询 |
| SERVICE_LOCATION | SLP 服务定位 |
| LLMNR | Link-Local Multicast Name |
| NETBIOS | NetBIOS |
| WSD | Web Services Discovery |
| BJNP | Canon BJNP 打印 |
| IPP | Internet Printing Protocol |
| XDMCP | X Display Manager |
| RX | RX Protocol |
| EGD | EGD 协议 |
| ATG | ATG 协议 |
| RMCP | Remote Management Control |
| DIAMETER | Diameter AAA |
| OCSP | OCSP 证书状态 |
| ACME | ACME 证书管理 |
| DNP3 | DNP3 |
| PTPV2 | Precision Time Protocol v2 |
| ROUGHTIME | Roughtime 时间同步 |
| IP_ICMP | ICMP |
| IP_ICMPV6 | ICMPv6 |
| IP_IGMP | IGMP |
| IP_PIM | PIM 多播路由 |
| IP_PGM | PGM 可靠多播 |
| IP_SCTP | SCTP |
| IP_GRE | GRE 隧道 |
| IP_ESP | IPSec ESP |
| IP_AH | IPSec AH |
| IP_EGP | EGP |
| IP_OSPF | OSPF |
| IP_IP_IN_IP | IP-in-IP 隧道 |

### 电子商务和购物 (10)

| 协议名 | 说明 |
|--------|------|
| AMAZON | Amazon 购物 |
| EBAY | eBay |
| ALIBABA | 阿里巴巴 |
| TAOBAO | 淘宝 |
| TEMU | Temu 购物 |
| SHEIN | SHEIN 购物 |
| YANDEX_MARKET | Yandex Market |
| SALESFORCE | Salesforce CRM |
| SHOPIFY | Shopify (via HTTP) |
| PAYPAL | PayPal (via HTTPS) |

### 安全和加密 (10)

| 协议名 | 说明 |
|--------|------|
| DNSCRYPT | DNSCrypt |
| DOH_DOT | DNS over HTTPS/TLS |
| OPENDNS | OpenDNS |
| AVAST | Avast 安全软件 |
| AVAST_SECUREDNS | Avast SecureDNS |
| CYBERSECURITY | 网络安全协议 |
| BITCOIN | Bitcoin 协议 |
| ETHEREUM | Ethereum 区块链 |
| MONERO | Monero 加密货币 |
| MINING | 加密货币挖矿 |

### 物联网和智能设备 (14)

| 协议名 | 说明 |
|--------|------|
| COAP | CoAP 物联网协议 |
| XIAOMI | 小米智能设备 |
| TPLINK_SHP | TP-Link 智能插座 |
| TUYA_LP | 涂鸦智能设备 |
| MATTER | Matter 智能家居标准 |
| KNXNET_IP | KNX 智能家居 |
| SAMSUNG_SDP | Samsung 设备协议 |
| TESLA_SERVICES | Tesla 车联网 |
| SONOS | Sonos 音响系统 |
| UBIQUITY | Ubiquiti 设备 |
| MERAKI_CLOUD | Cisco Meraki |
| NEST_LOG_SINK | Google Nest |
| CAN | CAN Bus 车载网络 |
| EASYWEATHER | 气象站协议 |

### 其他协议 (31)

| 协议名 | 说明 |
|--------|------|
| HTTP2 | HTTP/2 |
| WEBSOCKET | WebSocket |
| SOAP | SOAP Web 服务 |
| JSON | JSON over HTTP |
| FASTCGI | FastCGI |
| AJP | Apache JServ Protocol |
| SOCKS | SOCKS 代理 |
| GEARMAN | Gearman 任务分发 |
| RDP | Remote Desktop Protocol |
| VNC | VNC 远程桌面 |
| TEAMVIEWER | TeamViewer |
| ANYDESK | AnyDesk |
| NOMACHINE | NoMachine |
| RADMIN | Radmin 远程管理 |
| CITRIX | Citrix 虚拟化 |
| VMWARE | VMware 协议 |
| GIT | Git 版本控制 |
| GITHUB | GitHub 服务 |
| GITLAB | GitLab 服务 |
| WIKIPEDIA | Wikipedia |
| PASTEBIN | Pastebin |
| WAZE | Waze 导航 |
| ACCUWEATHER | AccuWeather |
| 1KXUN | 1KXUN |
| DATASAVER | 数据节省模式 |
| YOJIMBO | Yojimbo |
| UMAS | UMAS |
| ZUG | ZUG |
| EDGECAST | EdgeCast CDN |
| CACHEFLY | CacheFly CDN |
| FASTLY | Fastly CDN |

---

## 说明

### 为什么这些协议没有详细文档？

这些协议在 nDPI 的 `ndpi_dpi2json` 函数中**没有专门的 JSON 序列化代码块**。这意味着：

1. **仅识别协议名称**: 当检测到这些协议时，JSON 输出只包含协议名称
2. **无协议特定字段**: 不会输出协议特定的详细信息（如端口、版本、参数等）
3. **通用处理**: 使用 nDPI 的通用流信息处理

### JSON 输出示例

对于未文档化的协议，典型的 JSON 输出格式为：

```json
{
  "ndpi": {
    "protocol": "MYSQL",
    "category": "Database"
  }
}
```

仅包含协议名称和类别，不包含协议特定字段。

### 如何请求新协议文档

如果您需要为特定协议创建详细文档，请：

1. 确认该协议在 nDPI 源码中是否有 JSON 序列化实现
2. 查看 `src/lib/ndpi_utils.c` 的 `ndpi_dpi2json` 函数
3. 如果存在序列化代码，可以请求创建文档
4. 如果不存在，需要先在 nDPI 源码中实现序列化逻辑

### 相关资源

- **协议 ID 定义**: `src/include/ndpi_protocol_ids.h`
- **JSON 序列化实现**: `src/lib/ndpi_utils.c` (行 1530-2016)
- **已文档化协议列表**: `doc/protocols/README.md`
- **使用指南**: `doc/ndpi_dpi2json_guide.md`

---

## 更新历史

- **2026-01-22**: 初始版本，列出 437 个未文档化协议
