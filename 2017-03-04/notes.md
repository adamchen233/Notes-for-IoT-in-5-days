##今天开始看
- Introduction to Contiki

## 这章相对较偏向实践，理论不多，笔记上讲不会过多讲述具体的操作

Contiki is an open source operating system for the Internet of Things, it connects tiny low-cost, low-power microcontrollers 
to the Internet. contiki是一个开源系统，特点是能耗低、易用，程序由C语言编写，跑在motes上，还有一个模拟平台Cooja。

### Contiki的安装
前提：在虚拟机上跑，暂不考虑实际的烧入硬件

Contiki的安装可以直接git clone contiki的源码，也可以直接使用官方提供的instant Contiki虚拟机镜像，IoT in 5 days也有提供一个自己的虚拟机镜像。

然后介绍了2个平台，分别是RE-Mote和the Z1 platforms，简单地说就是前者好用功能强多少不好模拟，后者简单而且cooja完美模拟。

## contiki application大概长什么样

IoT in 5 days通过镜像中的example讲解contiki的应用大概是怎么样的，代码可以看 [这里](https://github.com/alignan/contiki/tree/iot-workshop/examples/zolertia/tutorial)

从输出hello world、应用 LED灯、到几种定时器TIME的简单使用；

几种定时器：

- Simple timer：时间到了，stimer_expirde（）会返回true，通常通过while(!timer_expired(&nt))来控制，缺点就是浪费CPU CYCLE
- Callback timer：回调定时器，时间到了就是调用设定好的函数，可以带参
- Event timer：事件定时器，时间到了会触发PROCESS_EVENT_TIMER事件
- Real time timer：实时定时器，和回调有点类似，但多了实时功能，去handles the scheduling and execution of real-time tasks

然后开始讲解contiki的process，这个感觉比较重点，稍微多做点笔记

### Processes in Contiki

首先，Contiki应用中通过`PROCESS_BEGIN()`和`PROCESS_END()`这2个宏指令去管理PROCESS。

contiki使用一种叫protothread的方式，在节省真正线程的开销下，来简化线程和实现类似于线程的效果。

`Libraries and applications in Contiki uses processes to run and to communicate with other modules, by creating its own processes
or using the already available. `库和应用通过使用process来和其他模块进行交互，可以自己创建，也可以使用现有的。

Contiki has two execution contexts: cooperative（协同） and preemptive（先制）.process是协同和连续的，中断（interrupt-button、sensor event）
和实时定时器是先制的；

![Execution contexts: processes and interrupts](https://raw.githubusercontent.com/adamchen233/Notes-for-IoT-in-5-days/master/pics/Execution%20contexts%20processes%20and%20interrupts.png)

几种方法去YIELD一个protothread
- PROCESS_YIELD()：不检查事件类型
- PROCESS_WAIT_EVENT_UNTIL：等待特定的event
- PROCESS_PAUSE()：自己给自己post一个停止时间，等待其他的processes，等其他的完成
- PROCESS_EXIT()：在达到PROCESS_END()之前，给自己一个PROCESS_EVENT_EXITED通知，提前结束process

protoThreadhen很有用，他可以让我们的应用各部分在各自的process中运行，更清晰（Cleaner）和有效率（efficient）。

各个process之间的通信、等待，基本就是结合上面几个宏函数、timer以及相关的事件，process可以向另一个process发送一个事件，具体看code；

到此为止，书里对PROCESS的介绍结束，更加详细的内容contiki os有给出，[链接在此](https://github.com/contiki-os/contiki/wiki/Processes)，下次会单独补充。

### Sensors
具体的使用看contiki的文档和代码例子吧，偏向硬件的东西看起来有点吃力···，毕竟本人没有任何电子硬件的基础，只能靠从代码中感悟了；
Sensor分为2种analogue（模拟的） or digital（数字的）；前者比如电压传感器；后者比如温度、3-axis accelerometer；
ADC ： (analogue to digital converter)

### General input/output pins (GPIO)
