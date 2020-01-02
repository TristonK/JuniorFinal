# 网络层：控制平面

## 5.2 路由选择算法

BGP协议报文使用TCP报文封装，RIP使用UDP，OSPF使用IP报文封装

路由选择算法的目的：从发送方到接收方的过程中确定一条通过路由器网络的好的路径

路由选择算法的分类：

* 集中式路由选择算法：用完整的全局的网络知识来计算，具有全局状态信息的算法叫做**链路状态算法\(LS\)**
* 分散式路由选择算法：路由器使用迭代、分布式的算法计算出最低开销。如**距离向量\(DV\)算法**
* 静态路由选择算法：路由随时间变化缓慢，通常人工配置
* 动态路由选择算法
* 负载敏感算法
* 负载迟钝算法：目前使用的\(RIP,OSPF,BGP\)都是负载迟钝的

### 5.2.1 链路状态路由选择算法

如OSPF算法，这通常由**链路状态广播\(link state broadcast\)**算法完成，节点广播的结果就是所有节点都有该网络的统一、完整的视图

一种链路状态路由选择算法是Dijkstra算法

```cpp
/*
* D(v) 到本次迭代，从源节点到目的节点v的最小距离
* p(v) 源节点到点v的最短路径的前一节点
* N‘ 节点子集，包含了源到其点最短路径已知的所有点
*/
Initialization:
    N’ = {u}
    for all nodes v
        if v is a neighbor of u
            then D(v) = c(u, v)
        else D(v) = ∞

Loop:
    find w not in N’ such that D(w) is a minimum
    add w to N’
    update D(v) for each neighbor v of w and not in N’:
        D(v) = min(D(v), D(w)+ c(w, v) )
    /* new cost to v is either old cost to v or known
    least path cost to w plus cost from w to v */
    until N’= N
```

### 5.2.2 距离向量路由选择算法

迭代的（一直要持续到邻居之间无更多信息交换为止）、异步的、分布式\(每个节点要从一个或者多个直接邻居接收某些信息，执行计算，然后把结果分发给邻居\)的算法

使用的协议有BGP,RIP

Bellman-Ford算法：$d\_x\(y\)=min\_v{c\(x,v\)+d\_v\(y\)}$

算法思路：每个节点从邻居节点处得到一个距离向量，他保存v的距离向量，然后使用Bellman-Ford方程更新自己的距离向量

* 可能遇到的问题：当DV算法稳定后某条链路开销突然改变，可能出现路由选择环路问题，节点间的最短路径相互依赖，导致结果在节点间不断的来回变换直到最后稳定，称为无穷计数问题
* 一种尝试解决的方式：增加毒性逆转：如果z通过y路由到达目的地x，那么z告诉y，z到x的距离无穷大。但是涉及三个或以上节点的环路毒性逆转无效

## 5.3 自治系统内部的路由选择：OSPF

为了解决1\)互联网规模大，所有路由器之间更新链路开销的负担是巨大的2\)ISP希望按照自己的意愿运行路由器或者对外部隐藏其网络的内部组织面貌，这两个问题可以通过将路由器组织进**自治系统\(AS\)**解决

自治系统AS由一组通常处在相同管理控制下的路由器组成，通常在一个ISP中的路由器以及互联他们的链路构成一个AS。一个自治系统由其全局唯一的AS号\(ASN\)所标识,AS号由ICANN区域注册机构分配

运行在一个自治系统内的路由选择算法叫做**自治系统内部路由选择协议**

OSPF:开放最短路优先，是一种链路状态协议，使用洪泛链路状态信息和Dijkstra最低开销路径算法。

使用OSPF时，路由器向自治系统内所有其他路由器广播路由选择信息，每当一条链路的状态发生变化后，路由器就广播链路状态信息，**即使不改变也要周期性\(至少30min一次\)的广播一次**。

OSPF的优点：

* 安全：所有的OSPF消息经过身份验证，能够配置简单的和MD5\(基于配置在所有路由器上共享秘密密钥，路由器对附加了秘密密钥的OSPF分组计算MD5散列值并包括在OSPF分组中，接收路由器根据预配置的秘密密钥计算MD5散列值与OSPF中携带的比较\)的鉴别
* 存在多条相同开销的路径时，允许使用多条路径
* 对单播和多播综合支持
* 支持在单个AS内的层次结构：允许AS内划分区域，有一个主干区域，每个区域运行自己的OSPF，如果要跨区域：区域1-&gt;主干区域-&gt;区域2

## 5.4 ISP之间的路由选择:BGP

因特网中，所有的AS运行相同的AS间路由选择协议，称为**边界网关协议\(BGP\)**

### 5.4.1 BGP的作用

在BGP中，分组时路由到CIDR化的前缀，每个前缀表示一个子网或者一个子网的集合，因此一台路由器的转发表将具有\(x,I\)的表项，其中x为前缀，I时路由器的接口之一的接口号

* BGP提供的手段
  * 从邻居AS获得前缀可达信息。BGP允许每个子网向因特网的其余全部部分通告他的存在
  * 确定到该前缀的最好的路由 

### 5.4.2 通告BGP路由信息

对于每个AS，每台路由器要么是**网关路由器**，要么就是**内部路由器**，网关路由器直接连接到其他AS中一台或者多台路由器，内部路由器只连接AS内部路由器或者主机

每队路由器使用179端口的半永久TCP连接来交换路由选择信息

* 跨越两个AS的BGP连接：外部BGP连接\(eBGP\)
* AS内部的BGP连接：内部BGP连接\(iBGP\)

传递可达信息：不断重复AS内部广播，网关传递到其他AS的过程

### 5.4.3 确定最好的路由

路由器通过BGP连接通告前缀时，会在前缀中引入一些BGP属性。前缀及其属性称为**路由**。两个比较重要的属性是**AS-PATH**和**NEXT-HOP**。其中AS-PATH包含了通告已经通过的AS列表，NEXT-HOP是AS-PATH起始的路由器接口的IP地址。

#### 热土豆路由选择

转发表中增加AS外部目的地的步骤：

* 从AS间协议学到经多个网关可到达子网x
* 使用AS内部的路由选择信息，决定到达每个网关的最低开销路径的开销
* 热土豆路由选择：选择具有最低最小开销的网关
* 从转发表确定通往最低开销的网关接口I，在转发表中加入表项\(x,I\)

热土豆选择：尽可能快的将分组送出其AS，使得他在AS内部的开销最小

#### 路由选择算法

如果有多条路径，顺序的按照以下规则消除直到只剩下一条：

* 路由被指派一个本地偏好作为属性值之一，具有最高本地偏好的路由被选择
* 如果多个具有相同的最高本地偏好，则从这些里面选择具有最短AS-PATH的路由\(其中距离测量是AS的跳数\)
* 剩下路由中使用热土豆路由选择方式，即最靠近NEXT-HOP路由器的路由
* 如果仍然剩下多条路由，则使用BGP标识符来选择路由

### 5.4.4 IP任播

多台服务器使用相同IP地址进行通告，路由器对于这多个通告，将会认为是到达同一个服务器的不同路径，在用户请求时选择较近的那个服务器

利用BGP实现，可用于DNS和CDN服务

### 5.4.5 路由选择策略

所有进入一个接入ISP网络的流量必然时以该网络为目的地，所有离开一个接入ISP的流量必然源于该网络

## 5.6 ICMP:因特网控制报文协议

ICMP：被主机和路由器用来彼此沟通网络层的信息，最典型用途时差错检测

ICMP会被作为IP有效载荷承载的，主机收到指明了上层协议为ICMP的数据报后会分解出内容给ICMP

ICMP报文有一个类型字段和一个编码字段，并且包含了引起该ICMP报文首次生成的IP数据报的首部和前8个字节\(方便确定引发差错的数据报\)

### ping的实现

ping程序发送一个ICMP类型8编码0\(对此的表述是回显请求\)的报文到指定主机，看到回显\(echo\)请求，目的主机发回一个类型0编码0的ICMP回显回答

### Traceroute的实现

traceroute：允许跟踪一台主机到另外一台主机之间的路由

源主机的traceroute向目的地主机发送一系列普通的IP数据报，这些数据报每个携带了一个具有不可达UDP端口号的UDP报文段，第一个数据报的TTL\(寿命\)为1，第二个为2，以此类推。该源主机为每个数据报启动定时器，第n个数据报到达第n个路由器的时候，第n台路由器观察到这个数据报的TTL刚好过期，按照IP协议规则，路由器丢弃这个数据报并发送一个ICMP警告给源主机\(类型11编码0\).该警告包含了路由器的名字与他的IP，当该ICMP报文返回源主机的时候，源主机从定时器得到往返时延，从ICMP报文得到第n台路由器的名字和IP地址
