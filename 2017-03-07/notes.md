## 很杂的笔记

看了部分理论之后开始一边做一边理解contiki，下面基本全是个人理解的内容，口语化严重···

首先，理清楚contiki、cooja、motes之类概念的关系，这些东西都是在IoT里的WSN里的，WSN就是无线传感器网络，由3部分设备组成，就是motes（也叫nodes），就是
节点，负责收集数据，数量大、能源少；然后是actuators，和motes差不多，也能收集数据，都是还有act的功能；base station，基站，负责收集，一般是网络设备，比
如边界路由器；这些设备组成一个local net，一般用的是6lowpan，因为这个协议简单耗能低，很符合IoT的特点，并且基于IPV6，泛用，而且地址多。通过边界路由器与外
界交互，这里还涉及一些ipv6和4的转换之类的内容；

然后contiki是一个开源操作系统，C写的，应用也是C写的，模式是protothread，简单又能清楚方便地实现多线程类似的效果。实际使用的之后都是将contiki及其应用
编译烧入硬件中（比如一些有传感器的小东西作为mote，比如路由器作为wsn中的路由器，硬件平台之间有区别，需要做适配，本人不玩硬件的所以这里跳略得比较多，只
有一些概念性认知）；

cooja是contiki自带的一个模拟器（java写的，有图形界面，比较还用但是好像使用说明很少，加上个人对硬件了解比较少，所以略尴尬），可以模拟一些硬件并且编译
放入应用，然后再特定的环境跑起来，还有模拟比如按button，观察led灯，还能获得运行时的log，另外cooja script是一种运行测试脚本，用js写的，通过JAVA和JS
之间的切换，可以做到一些自动化的测试，比如设置一个timeout，到达时间之后表示测试超时，测试失败；比如可以获取msg，写入文件或者日志；可以自己生成一个msg，用
来让下一步的wait的唤醒，达到一个等几秒的效果；还有获取某ID的mote设备的引用，可以用脚本去实现按button的效果，（而不是在cooja的图像界面里去手动点），可以获
取mote的各种事件、信息并且打印；

==================

根据目前的实践，一般使用cooja的模拟就是先完成环境的模拟，一般至少要有一个border router，这里摘抄下一个cooja启动border router的步骤： 
[来源](https://github.com/ejoerns/contiki-inga/wiki/Cooja-Border-Router-Setup), 太感谢这个了！！！

```
Start new Cooja simulation
Create new inga mote with border router code (examples/ipv6/rpl-border-router/border-router.c)
Start Serial Socket Server plugin
Start simulation
Connect with tunslip6 tool (tools/tunslip6)

sudo ./tunslip6 -v3 -a 127.0.0.1 aaaa::1/64

Open the border router HTML interface with a webbrowser (IP should be [aaaa::200:0:0:1]).
```

注意记录router的全局IPV6地址，经常用，完成这一步之后建议ping6一下看是否配置成功，OK之后可以在浏览器里浏览wsn中的邻居节点（实现里有内置的HTTP服务器）；

然后就是添加各种motes和执行器，并且带上应用，配置下具体环境，（比如距离、温度之类的），就可以开始跑了；

这时你可以开始写一些脚本去完成特定的测试，比如你要测试按某个mote的button，然后启动udp广播，并且打印出所有motes的udp的信息，一定时间后测试结束；这整
个过程就用一个脚本完成，而不是在cooja里点点点；

==========


然后是几个常见的应用类型，在contiki的examples里有示例代码的，

最简单的比如mote之间的连接传输信息、定时触发sensor收集数据、udp广播、TCP连接；

稍微实用点的比如CoAP和MQTT，这2个会单独拿出来说。


这里简单提一下，CoAP个人理解就是低电不靠谱环境下的（很多时候用在IOT）类似于HTTP的应用层的协议，模式是REST，就是把各种东西（比如常规的数据、或者操
作、传感器都可以）资源化，然后用GET\PUT\POST\DELECT之类的 操作去完成特定的功能；

然后MQTT就是一个发布、订阅模式的信息传递应用层协议，中间的东西叫broker，前后发布有发布者和订阅者，有3个级别的QoS,分别是发布者发布一条消息时，订阅者
最多收到一条、最少收到一条、正好就收到一条，contiki例子里完成了QoS0和1，github里有dalao写了更完善的MQTT库，完成了QoS2，这个之后应该会稍微研究下。

===============
暂时就酱。
