##今天看的章节
- Introduction to 6LoWPAN

## 6LoWPAN
首先，IoT中使用的通信技术需要满足2个基本要求，一个是要求能满足IoT网络的低能耗、长生命周期等特点，另一个是要保证兼容性（即已经是一种广泛应用的技术）；
所以，The IETF has different working groups (WGs) developing standards to be used by WSN:（IETF：Internet engineering task force）

1. 6LoWPAN（IPv6 over Low-power Wireless Personal Area Networks），defines the standards for IPv6 communication over the IEEE
802.15.4 wireless communication technology.作为IPV6和LLN的中间适配层，
2. roll（Routing Over Low power and Lossy networks [roll].）因为LLN下对Routing有特点的要求，所以不能用原来的routing solutions
3. 6lo （IPv6 over Networks of Resource-constrained Nodes）This WG deals with IPv6 connectivity over constrained node networks.
It extends the work of the 6lowpan WG, defining IPv6-over-foo adaptation layer specifications using 6LoWPAN for link layer 
in constrained node networks.

6LoWPAN is the basis of the work carried out in standardization at IETF to communicate constrained resources nodes in LLNs using IPv6.
The work on 6LoWPAN has been completed and is being further complemented by the roll WG to satisfy routing needs and the 6lo WG to 
extend the 6lowpan standards to any other link layer technology.
6LoWPAN是使用IPV6的资源受限的网络交流节点（LLN）的基础，后2个WG是后续，roll是满足特点的路由要求，6lo是拓展6LoWPAN的标准。

### Overview of LoWPANs
LLNs（Low-power and lossy networks）是一种资源受限的网络类型，节点高度受限+low-power radio links。
Limited Processing Capability、Small Memory Capacity、Low Power、Short Range和Low Cost是LLNs下设备的特点。

A LoWPAN is a particular instance of an LLN, formed by devices complying with the IEEE 802.15.4 standard.LoWPAN是特殊的LLN。

LoWPAN本身的特点：

1. Small packet size
2. IEEE 802.15.4 defines several addressing modes
3. Low bandwidth
4. Topologies include star and mesh
5. Large number of devices expected to be deployed during the lifetime of the technology
6. Devices within LoWPANs tend to be unreliable due to variety of reasons: uncertain radio connectivity, battery drain, 
device lockups, physical tampering, etc.
7. Sleeping mode

### About the use of IP on LoWPANs
6LoWPAN是一种基于IPV6的LoWPANs，LoWPANs使用IP协议的好处大致有：IP普遍正常、基于IP的技术很成熟很多、IP易于理解（大家都了解）、IPV6地址多。

问题/挑战：IPV6的header长度不适合LoWPANs，需要压缩、LoWPANs中经常需要的是星/网状的拓扑、服务发现和安全性。

### 6LoWPAN
6LoWPAN是一种中间层协议，下层是physical and link layers，负责LoWPAN中设备的物理连接，上层是网络层（这里是IPv6），提供了很多好处
（上面已经分析过了），之所以要有6LoWPAN这个中间层，是因为在IoT、WSN环境下，他的下层和上层之间有一些需要解决的问题：

1. Fragmentation and Reassembly layer，分割和重组，主要和MTU有关；
2. Header Compression，header压缩，下层要求packet最大为81八位字节，而IPV6的基础头就有40个八位字节了，还不包括拓展头和上位协议
（TCP、UDP之类的）的header，所以这一步是必要的；
3. Address Autoconfiguration，为设备分配IID；
4. Mesh Routing Protocol，一个为网状拓扑网络路由的协议，需要修改或者新定义；
5. IEEE 802.15.4 defines four types of frames: beacon frames, MAC command frames, acknowledgement frames and data frames.
6. Multicast ，在IEEE 802.15.4标准下是不原生支持广播的，所以中间层要负处理IPV6的广播
等

所以6LoWPAN的适配格式，就是在有限的形式下携带IPV6的datagram，还要考虑有限的带宽、内存等。

![6LoWPAN headers](https://raw.githubusercontent.com/adamchen233/Notes-for-IoT-in-5-days/master/pics/6LoWPAN%20headers.png)

- Mesh Addressing support sub-IP forwarding,负责子网转发
- A Fragmentation header to support the IPv6 minimum MTU requirement.超过MTU时与分割有关的信息
- A Broadcast Header to be used when IPv6 multicast packets must be sent over the IEEE 802.15.4 network.实现IEEE 802.15.4下的广播
- Stateless header compression for IPv6 datagrams，原IPV6 header和上层的压缩形式。

### IPv6 Interface Identifier (IID)

IEEE 802.15.4标准下设备可以有2个地址：IEEE EUI-64 address（必要），16-bit short addresses（非必要，PAN中使用）

### Header Compression
有2中编码格式被用于IPV6 packet的压缩：LOWPAN_IPHC 和 LOWPAN_NHC。

假设6LoWPAN默认情况下：

1. 版本是V6；
2. Traffic Class和Flow Label全为0；
3. Payload Length可以被下层感知到；
4. Hop Limit是广为人知的值；
5. Addresses可表示为link-local prefix的形式；
6. Addresses IID可以直接从2中地址形态中得到；
由实际的packet与预设的情况有多不匹配，决定在in-line中带上不匹配的项；

![Header Compression](https://raw.githubusercontent.com/adamchen233/Notes-for-IoT-in-5-days/master/pics/Header%20compression.png)
- TF: Traffic Class, Flow Label.
- NH: Next Header.
- HLIM: Hop Limit.
- CID: Context Identifier Extension.
- SAC: Source Address Compression.
- SAM: Source Address Mode.
- M: Multicast Compression.
- DAC: Destination Address Compression.
- DAM: Destination Address Mode.

各个字段具体怎么决定6LoWPAN的工作的，感兴趣可以看原书，笔记里就不复制粘贴了
