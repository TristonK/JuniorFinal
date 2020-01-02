# 计算机网络与因特网

## 1.1 什么是因特网

回答什么是因特网：

* 因特网的具体构成，即因特网的基本硬件与软件组件
* 根据为分布式应用提供服务的联网基础设施来描述

### 1.1.1 具体构成描述

主机或者端系统: 与因特网相连的设备

端系统通过**通信链路**\(不同类型的物理媒体组成，包括同轴电缆、铜线、光纤和无线电频谱\)与**分组交换机**\(常见类型有路由器和链路层交换机，其中链路层交换机常用于接入网中，而路由器通常用于网络核心中\)连接在一起

链路的传输速度：比特/秒\(bit/s或者bps\)

分组：端系统向其他端系统发送信息时，发送端系统将数据分段，并为每段加上首部字节，由此形成的数据包叫做**分组**

一个分组经过的一系列通信链路和分组交换机称为通过该网络的**路径**

因特网服务提供商：ISP

每个ISP就是一个多台分组交换机和多段通信链路组成的网络

端系统、分组交换机和其他因特网部件要运行一系列协议，这些协议控制因特网中信息的接收和发送。TCP\(传输控制协议\)和IP\(网际协议\)是最重要的两个协议。因特网的主要协议统称为TCP/IP协议。

因特网标准由IETF研发，IETF的标准文档被称为**请求评论\(RFC\)**.但也有其他组织在研发，如IEEE 802 LAN/WLAN提出的以太网和无线WIFI的标准

### 1.1.2 服务描述

分布式应用程序：设计多个相互交换数据的端系统

因特网应用程序运行在端系统上而不在网络核心的分组交换机\(加速数据交换\)上

端系统提供了**套接字接口**，规定了运行在一个端系统上的程序请求基础设施向另一个端系统上的特定目的地程序交付数据的方式。

### 1.1.3 什么是协议

**协议**定义了在两个或多个通信实体之间交换报文的格式与顺序，以及报文发送和/或接收一条报文或其他事件所采取的动作。

## 1.2 网络边缘

端系统位于网络边缘

主机有时进一步分为两类：主机与服务器

### 1.2.1 接入网

接入网：端系统物理连接到其边缘服务器的网络

边缘路由器：端系统到任何其他远程端系统路径上的第一台路由器

#### 家庭接入网

* 数字用户线\(digital subscriber Line,DSL\)
  * 从本地电话公司获取，ISP：本地电话公司
  * DSL调制调解器利用现有的电话线\(双绞铜线\)与位于电话公司本地中心局\(CO\)中的数字用户接入复用器\(DSLAM\)交换数据。DSL调制调解器得到数字数据后转化为高频音，以通过电话线传输给本地中心局。
  * 使用频分复用技术\(不同频率编码\)使得电话呼叫和因特网连接共享一根线
* 电缆因特网接入\(cable Internet access\)
  * 使用有线电视公司的有线电视基础设施，应用光纤和同轴电缆，称为混合光纤同轴系统\(HFC\)
  * 需要特殊的调制调解器：电缆调制调解器
  * 电缆调制调解器系统\(CMTS\)将HFC分为上行和下行两个信道
  * 共享广播媒体，故而需要一个分布式多路访问协议来细条传输和避免碰撞
* 光纤到户\(FTTH\)
  * 从中心局直接到家庭提供一条光纤路径
  * 直接光纤：从本地中心局到每户设置一根光纤，或者中心局出来的每个光纤多个家庭共享一直到接近家庭的位置光纤猜分成每户一根。分配方案有两种
    * 主动光纤网络\(AON\)：本质上是交换因特网
    * 被动光纤网络\(PON\) ：每个家庭有一个光纤网络端接器\(ONT\),由专门的光纤连接到附近的分配器，分配器把一些家庭集结到一根共享的光纤，这根光纤连接本地电话和公司的中心局中的光纤线路端接器\(OLT\)，该OLT提供了光信号和电信号之间的转换。
* 卫星链路

#### 企业\(和家庭\)接入

局域网:LAN

以太网是最流行的局域网接入技术

基于IEEE 802.11技术的无线LAN接入：WiFi

#### 广域无线接入：3G与LTE

### 1.2.2 物理媒体

物理媒体包括了导引型媒体\(电波沿着固体媒体前行，如光缆、双绞铜线或同轴电缆\)和非导引型媒体\(在空气或者外层空间传播，如无线局域网\)

## 1.3 网络核心

网络核心：互联网端系统的分组交换机和链路构成的网状网络

### 1.3.1 分组交换

端系统彼此交换**报文\(message\)**。源将长报文分成较小的数据块，称之分组。每个分组经过通信链路和分组交换机交换，**分组以等于该链路最大传输速率的速度通过通信链路**。假设传输 L 比特的分组，链路速度为 R bps，那么传输该分组时间为 L/R 秒

#### 存储转发传输

多数分组交换机在链路的**输入端**使用**存储转发传输**机制，即在交换机能够开始向输出链路传输该分组的第一个比特之前须接收到整个分组。

如果不考虑传播时延，通过N哥速率为R的链路组成的路径，端到端时延为N\(L/R\)

#### 排队时延与分组丢失

每个分组交换机有多条链路，对于每条链路分组交换机有一个**输出缓存\(也叫输出队列\)**，用于存储路由器准备发完那条链路的分组。如果链路正在传输其他的分组，那么该分组必须在**输出缓存**中等待。如果到达的分组发现缓存已经充满了，那么会出现**丢包**现象，到达的分组或者队列中的分组之一将被丢弃。（接近满时概率丢包）

#### 转发表与路由选择协议

每个端系统有一个IP地址，路由器有一个**转发表**，将目的地址或目的地址的一部分映射成输出链路。分组到达时，利用目的地址搜索转发表，找到出链路，路由器将这个分组导向该出链路。

**路由选择协议**用于自动的设置转发表。

### 1.3.2 电路交换

端系统的通信会话期间，预留了端系统间沿路径通信所需要的资源\(如缓存、链路传输速率\)。

例子:传统的电话网络

电路交换网络在能够发送信息之前，必须在发送方和接收方之间建立一条**连接**，此时沿着发送方和接收方之间路径上的交换机都会为该连接维护连接状态。该连接称为一条**电路\(circuit\)**。创建电路时，在连接期间为该网络预留了恒定的传输速率，发送方以**确保的恒定速率**向接收方传送数据。

复用现象：

* 频分复用\(FDM\):频谱由所有连接共享，连接期间每条连接专用一个频段。频段宽度称为**带宽**
* 时分复用\(TDM\):时间划分为固定的帧，每个帧划分成固定数量的时隙，创建连接时，网络在每个帧中指定一个时隙提供给这个连接单独使用。电路的传输速率为帧速率乘以一个时隙中的比特数。
* TDM较FDM的好处在于FDM 需要相应的硬件设施来将信号转化为合适的频段，而TDM 只需要选择帧中合适的时隙就

  可以了。

**分组电路和交换电路的对比**：

* 电路交换电路是确保传输的，而分组交换是尽最大努力以实时方式交付分组但是不做任何保证
* 分组交换的优点：
  * 1\)提供了更好的带宽共享，电路交换预先分配使得已分配而不需要的链路时间未被利用，而分组交换按需分配使用电路
  * 2\)比电路交换更简单有效，创建端到端电路和预留带宽是复杂的，需要复杂的信令软件来协调
* 但是分组时延的端到端时延是可变和不可预测的，而电路交换保证能以确定速率传输

### 1.3.3 网络的网络

除了第一层ISP之外，任何ISP可以选择多宿,即选择多个提供商ISP连接

## 1.4 分组交换网中的时延、丢包与吞吐量

吞吐量：每秒可以传送的数据量

### 1.4.1 分组交换网中的时延概述

节点总时延：

* 节点处理时延：检查分组首部和决定分组导向何处，检查比特级别差错，处理后导向路由器B之前的队列
* 排队时延：在链路上等待传输。微秒到毫秒级。
* 传输时延：将所有分组的比特推向链路需要的时间，为L/R。毫秒级到微秒级
* 传播时延：链路起点到路由器B的传播时延

### 1.4.2 排队时延和丢包

假设a表示分组到达队列的速率\(分组/秒\)，L表示分组的大小\(bit\),那么La表示比特到达队列的平均速率\(bps\). 假设R表示传输速率\(即队列中推出比特的速率，bps\)。

定义**流量强度**：La/R。在流量工程中，流量强度不能大于1.随着流量强度接近1，平均排队时延快速增长。

### 1.4.3 端到端时延

如果不考虑排队时延 $$d_{end-end} = N(d_{proc}+d_{trans}+d_{prop})$$

### 1.4.4 吞吐量

瞬时吞吐量：接收文件的瞬时速率

平均吞吐量：F/T（接收F比特用了T秒\)

瓶颈链路：吞吐量是各个子链路的最小值

## 1.5 协议层次及其服务类型

### 1.5.1 分层的结构体系

#### 协议分层

每层向他的上一层提供服务，即所谓一层的**服务模型**，每层都在该层执行操作或者直接使用下层的服务来提供服务。

协议栈：各层的所有协议

* 应用层
  * 网络应用层序及他们的应用层协议停留的地方\(HTTP,SMTP,FTP\)
  * 应用层的信息分组：**报文\(message\)**
* 运输层
  * 在应用程序端点之间传送应用层报文
  * TCP：面向连接的服务\(确保传递与流量控制\)，提供拥塞控制
  * UDP：无连接服务,不提供不必要服务，没有可靠性，没有流量控制与拥塞控制
  * 分组：**报文段\(segment\)** 
* 网络层
  * 运输层协议向网络层细一递交运输层报文段与目的地址，网络层负责将**数据报\(datagram\)**的网络层分组从一台主机移动到另一台主机
  * 网际协议：IP协议
  * 路由选择协议 
* 链路层
  * 网络层将数据报下传给链路层，链路层沿着路径将整个帧传给下一个节点，在下一个节点，链路层将数据报上传给网络层
  * 链路层分组：**帧\(frame\)** 
* 物理层
  * 将帧中的比特从一个节点移动到另一个节点，这一层的协议与链路的实际传输媒体相关

#### OSI模型

* 应用层
* 表示层
  * 使得通信的应用程序能够解释交换数据的含义
* 会话层
  * 数据交换的定界与同步功能，包括了建立检查点和恢复方案的方法
* 运输层
* 网络层
* 链路层
* 物理层

### 1.5.2 封装

链路层交换机：实现了1-2层\(尽管不能识别IP地址，但是可以识别第二层地址如以太网地址\)

路由器：实现了1-3层

主机：实现了所有的5个层次

每一个分组：首部字段+有效载荷字段\(上一层的分组\)

运输层首部信息：允许接收端运输层向上向适当的应用程序交付报文的信息；差错位检测信息

网络层首部信息：源和目的端系统系统地址

链路层首部信息：-

## 1.6 面对攻击的网络

僵尸网络：

* 病毒：需要某种形式的用户交互
* 蠕虫：不需要明显用户交互就能进入设备

拒绝攻击服务\(DOS\):使得基础设施部分不能由合法用户使用

* 弱点攻击：向目标主机运行的易受攻击的应用程序或者操作系统发送制作精细的报文
* 带宽洪泛：向目标主机发送大量分组
* 连接洪泛：向目标主机创建大量的半开或者全开的TCP连接

分组嗅探：记录每个流经的分组副本

IP哄骗：将具有虚假源地址的分组注入因特网（解决方式：端点鉴别）
