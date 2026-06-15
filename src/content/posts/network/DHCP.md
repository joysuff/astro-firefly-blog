---
title: DHCP详解
published: 2026-06-15
description: 主要介绍DHCP的工作原理、作用以及常见配置
tags: [网路协议, DHCP]
category: 网络协议
draft: false
image: api
---

# 简介
动态主机配置协议DHCP（Dynamic Host Configuration Protocol）是一种用于集中对用户IP地址进行动态管理和配置的技术。
# 作用及优点
- 实现地址的动态分配，为主机提供IP地址、网关、DNS服务器地址、掩码、租期等的分配。
- 简化地址配置流程，提高地址的利用率，统一进行管理，简化维护流程。

# 原理
## DHCP服务器
DHCP服务器负责从地址池中选择地址分配至DHCP客户端，还可以为DHCP客户端提供其他网络参数，如默认网关地址、DNS服务器地址。DHCP服务器可以接收处理来自**本网段**或**跨网段**（由DHCP中继转发）的DHCP请求报文。

## DHCP客户端
DHCP客户端发送DHCP请求报文、向DHCP服务器请求获取IP地址等网络参数的设备。手机、电脑等。

> [!CAUTION] 注意
> DHCP客户端广播发送请求报文，位于同一网段的DHCP服务器能够接收到请求报文。若服务器与客户端不在同一网段就需要DHCP中继来完成客户端和服务器之间的报文转发。

## DHCP中继
DHCP中继负责转发DHCP服务器和DHCP客户端之间的DHCP报文，协助DHCP服务器向DHCP客户端动态分配网络参数的设备。

# 常见DHCP报文
- `DHCP Discover`：客户端寻为找DHCP服务器**广播**发送的报文。
- `DHCP Offer`：DHCP服务器对`DHCP Discover`报文的相应，携带了各种配置信息
- `DHCP Request`：
    - 客户端初始化后，发送**广播**的`DHCP Request`报文来回应服务器的DHCP Offer报文
    - 客户端重启后，发送**广播**的`DHCP Request`报文来确认先前被分配的IP地址等配置信息
    - 当客户端已经和某个IP地址绑定后，发送`DHCP Request`**单播**或**广播**报文来更新IP地址的租约
- `DHCP Ack`：服务器对客户端的`DHCP Request`报文的确认响应报文，客户端收到此报文后，才真正获得了IP地址和相关的配置信息
- `DHCP Nak`：服务器对客户端的`DHCP Request`报文的拒绝响应报文。例如DHCP服务器收到`DHCP Request`报文后，没有找到相应的租约记录，则发送`DHCP Nak`报文作为应答，告知DHCP客户端无法分配合适IP地址
- `DHCP Decline`：当客户端发现服务器分配给它的IP地址发生冲突时会通过发送此报文来通知服务器，并且会重新向服务器申请地址。
- `DHCP Release`：客户端可通过发送此报文主动释放服务器分配给它的IP地址
- `DHCP Inform`：DHCP客户端获取IP地址后，如果需要向DHCP服务器获取更为详细的配置信息（网关地址、DNS服务器地址），则向DHCP服务器发送`DHCP Inform`请求报文。

# 租期更新
- 当租期到达50%（T1）时，客户端会以**单播**的形式向服务器发送`DHCP Request`报文，请求更新IP地址租期。
    - 如果收到`DHCP Ack`，则租期更新成功
    - 如果收到`DHCP Nak`，则发送`DHCP Discover`报文获取新的IP地址
- 当租期达到87.5%（T2）时还没有收到服务器的应答，客户端会自动以**广播**的方式向服务器发送Request报文，请求更新IP地址租期
    - 如果收到`DHCP Ack`，则租期更新成功
    - 如果收到`DHCP Nak`，则发送`DHCP Discover`报文获取新的IP地址
- 如果租期时间到时都没有收到服务器的回应，客户端停止使用此IP地址，重新发送DHCP Discover报文请求新的IP地址。

# DHCP Snooping
## 简介
DHCP Snooping是DHCP的一种安全特性，用于保证DHCP客户端从合法的DHCP服务器获取IP地址，并记录DHCP客户端IP地址与MAC地址等参数的对应关系，防止网络上针对DHCP攻击。

## 原理
开启DHCP snooping的设备将用户的请求报文通过**信任端口**发送给合法的DHCP服务器，之后设备根据DHCP服务器回应的`DHCP ACK`报文信息生成**DHCP Snooping绑定表**。后续设备再从开启了DHCP Snooping的接口接收用户发来的DHCP报文时，会进行匹配检查，能够有效防范非法用户的攻击。

## 接口分类
- **信任接口**：信任正常接收DHCP服务器响应的`DHCP ACK`、`DHCP NAK`和`DHCP Offer`报文。
- **非信任接口**：在接收到DHCP服务器响应的`DHCP ACK`、`DHCP NAK`和`DHCP Offer`报文后，丢弃该报文。非信任接口可以**发送**客户端请求
> [!CAUTION] 注意
> 开启DHCP Snooping之后，所有接口都默认是非信任接口

## DHCP Snooping绑定表
DHCP Snooping绑定表记录了DHCP客户端IP地址与MAC地址等参数的对应关系，通过对报文与DHCP Snooping绑定表进行匹配检查，能够有效防范非法用户的攻击。

为了保证设备在生成DHCP Snooping绑定表时能够获取到用户MAC等参数，DHCP Snooping功能需应用于二层网络中的接入设备或第一个DHCP Relay上。

# 配置命令（华为设备）
## 创建地址池
```bash
ip pool v10 
  gateway-list 10.1.10.254 
  network 10.1.10.0 mask 255.255.255.0 
  lease day 0 hour 2 minute 0 
  dns-list 8.8.8.8
```
## 开启全局模式
```bash
interface GigabitEthernet0/0/0 
  ip address 10.1.11.1 255.255.255.0 （DHCP地址） 
  dhcp select global
```
## 中继配置
```bash
interface Vlanif10 
	dhcp select relay               # 开启中继 
	dhcp relay server-ip 10.1.11.1  # 设置服务器地址
```



