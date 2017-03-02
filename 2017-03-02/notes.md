## 今天就看了书的前两个章节，分别是
- Introduction to IoT.
- Introduction to IPv6.

## 对IoT的介绍

总结上，感觉这句讲得比较生动`The expectation is that of interconnecting millions of islands of smart networks enabling access to the 
information not only “anytime” and “anywhere” but also using “anything” and “anyone” ideally through any “path”, “network” and “any service”. `
物联网的期望的是互连数百万智能网络岛屿，使得他们不仅是“任何时间”和“任何地方”可以互联，而且通过任何路径和网络实现“任何东西”的互联。就是说物联网较之以
前的互联网，一个明显不同的是互联的范围更大了，从原来的电子设备与人，扩展到了所有东西，而要实现这一点，就需要赋予物品互联并且交互的能力，所以在物联网
中，sensing, identification and positioning都是设备基础/重要的能力，同时，还要endowed with an IP address to become smart objects, capable of 
communicating让东西变得智能。

接下来书介绍了IoT的Layered Architecture，一共4层，底层Device layer，设备层，顾名思义，就是包括传感器、执行器、RFID和网关等设备所在的层，在这里负责
搜集信息以便后续的进一步处理和分析；第二次是Network layer，提供设备层搜集来的信息传输/传递和networking的能力，传到处理中心；第三层是support layer，
中间层，过渡，隐藏底层的复杂性，为高层提供通用和特制的service，比如以DB这种常见的储存形式；最上层是application layer，就是具体的应用了。

然后开始讲“无处不在的网络”的概念，引出WSN（wireless sensor networks）
`A Wireless Sensor Network (WSN) is a self-configuring network of small sensor nodes (so-called motes) communicating among them using 
radio signals, and deployed in quantity to sense the physical world.`无线传感器网络就是一个有小型传感器节点（motes）组成的自配置网络，
他们之间通过radio信号交流，motes一般数量可观，分布在物理世界。

所以motes其实就是WSN中的传感器节点，他们具有有限的计算能力、有限的内存、无线电交流设备、低能源和一个或多个的传感器。这5个就是motes的一个主要元素。
对于motes的传感器分类，一般分为3种，即物理类Physical（温度湿度压强等）、化学类Chemical（氧气含量、pH等）、生物类Biological（微生物含量等）。而且
对于传感器在在物联网中有2个比较重要的指标：Field-Readiness和Scalability，前者叫野外准备（？），大概就是指此类传感器在户外/各种环境下的工作情况下；
后者叫可拓展性，是指在传感器是否足够小和便宜，能过实现数量较大的分布式搜集系统。书中有表格，感兴趣可以去看看，基本是物理类最好，化学类次之，生物类较差。

然后WSN的技术特点，或者说是优势，
- 无线 Wireless Networking，节省成本，一次投放，一直工作
- 自组织 Self-organization，motes之间一起组成热点网络，无需前置的网络环境，所以适合在各种环境下工作
- 耗能低 Low-power

然后是MSN的应用，比如动物跟踪、森林防火之类的，感兴趣阅读原书。

然后是MSN中的几种ROLE，
- Sensor nodes，就是传感器节点，负责收集信息，具有相互交流的能力
- Sink nodes，也叫base station，基站，负责收集sensor node收集来的数据，与网关互联，一般不具备sensor的能力 
- Actuators， 执行器，用来控制环境的，从某个开关或者某个sensor信号，来执行某些动作，如发光发声。

## 对IPv6的介绍
首先IPv6就是Internet Protocol version 6，一句话总结觉得这句话比较好
`As part of this "everything-connected-to-Internet" is the IoT, so now you know why you are reading this chapter about IPv6, 
the last version of the Internet Protocol. In other words, today, the easiest way to send and receive data is by means of 
the standards used in the Internet, including IP.`作为连接所有东西的物联网的一部分，你现在知道了为什么要在这一章介绍IPv6了。换句话来说，
在今天，实现收发数据最简单的方式就是通过Internet的已有的标准，也包括IP。

IPV6的部分稍微有点基础，所有就直接写点要点吧（IP的历史就不说了）。
书里的分层用的是Physical、Link、Network、Transport、Application的5层，然后讲了从应用层到物理层的依次封装以及之后反向的分发。

然后是IPv6的header，和V4做了点比较（域更少8-12更简洁，长度40字节更快，地址128位更多），另外除了基础的header域，还有一些extension headers，
如hop by hop、security等，依序固定，来提高更复杂灵活强大的功能。

然后就是IPV6的地址，分类上有Unicast (one-to-one)、Multicast (one-to-many)、Anycast (one-to-nearest)和Reserved。

形式上，每个地址有8 group，每个group有16个bits，组成了4个nibble，均用十六进制表示，所以一个128bits，还有几条简写跪着，就是左0可以缩，连续的0000可
以合并但是只能合并一个不然会混乱。例子： 
- 2001:0db8:4004:0010:0000:0000:6543:0ffd 
- 2001:db8:4004:10:0:0:6543:ffd 
- 2001:db8:4004:10::6543:ffd

还有一种网段的前缀写法，2001:db8:1::/48，表示前48位固定，后80位随意，并且在LAN中，经常用* A /64 prefix is always used in a LAN 
(Local Area Network) . *The rightmost 64 bits, are called the interface identifier (IID) because they uniquely identify
a host’s interface in the local network defined by the /64 prefix.，64位，前半为network id，后半为interface id（IID），

2个特殊IP ::/128 unspecified address，表示没有地址可用；::1/128 loopback address，表示本机

IPv6中有3中分配地址的方式，手动Statically，动态DHCPv6 和 无状态自动配置SLAAC（Stateless address autoconfiguration），第三个比较特殊，
为V6独有的方式，具体有机会详细写写过程。

中途安利了一把wireshark。

最后，说了IPV6的几种情景，
- 设备、路由、ISP都支持V6，皆大欢喜，直接全场用V6，所以叫Native IPv6 Connectivity
- 设备、路由支持，但是ISP不支持V6，这时候在路由出使用一种6IN4的tunnel，将V6的包封装成V4的
- 设备支持，但是路由和ISP不支持，在中间加支持V6的路由，替代原来的路由位置，然后用法和第二条差不多

That`s all.
