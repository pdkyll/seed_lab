---
show: step
version: 0.1
enable_checker: true
---

#第三章 显示过滤器的用法

##一、实验简介

本节涵盖以下主题：

- 显示过滤器简介；

- 配置Ethernet、ARP、主机及网络过滤器；

本章会讲解如何操纵Wireshark显示过滤器。显示过滤器应用于Wireshark抓取数据包之后（此时，Wireshark抓到的数据可能已经经过了抓包过滤器的过滤），使用它的目的是要让Wireshark按需显示已经抓取到的部分数据。

可根据以下限定规则，来配置Wireshark显示过滤器，对已经抓取到的数据包做进一步的精挑细选。
> - 根据某些参数，比如，IP地址、TCP/UDP端口号、URL或某台服务器的名称。
> - 根据某些条件，像“TCP目的端口号在1000～2000之间”或“数据包长度不应长于1000字节”这样的描述，都可以算作条件。
> - 根据某些现象，比如，TCP重传、TCP重复确认、怪异的TCP确认方式、数据包中某些原本应该置0的标记位实际却置1、抓包视图中出现了协议错误代码等现象。
> - 根据应用程序参数，比如，短消息服务（Message Service，SMS）的始发地和目的地号码，或服务消息块（Server Message Block，SMB）服务器名称等。

通过网络传送的任何数据都可以过滤，在过滤时，还可以根据过滤条件生成相关统计信息和图形 。

本章会介绍Wireshark显示过滤器的各种配置方法，包括通过预制菜单配置、从数据包显示栏内截取、在Filter输入栏内直接输入过滤语句等。


> **注 意**	
> 请别忘了，捯饬显示过滤器时，所有数据都已被Wireshark抓获，显示出的数据只是经过显示过滤器筛选而已。也就是说，抓包文件依旧保存Wireshark抓到的所有原始数据，但可在应用显示过滤器之后，让Wireshark把经过筛选的数据单独保存为一个新的文件。	 


##二、配置显示过滤器

配置显示过滤器时，可选择以下几种方式。
>-  借助于显示过滤器窗口。
>-  在显示过滤器工具条的Filter输入栏里直接输入过滤语句（与此同时，Wireshark还可以照常抓包；此法注定会成为读者以后最常用的筛选所抓数据包的方法）。
>-  在抓包主窗口的包结构显示栏中，将数据包的某个属性值选定为显示过滤器的过滤条件。
>-  通过tshark或wireshark命令行来配置（详见附录） 。

本章只介绍前三种显示过滤器的配法。

###2.1 配置准备   

每条显示过滤器通常都是由若干原词构成，原词之间通过连接符（如and或or等）连接，原词之前还可以添加not表示相反的意思，其语法如下所列。
[not] Expression [and|or] [not] Expression...

其中，Expression可以为任意原词形式的过滤表达式，比如，表示源IP地址的ip.src==192.168.1.1、表示TCP SYN标记位置1的tcp.flags.syn==1、表示发生TCP重传现象的tcp.analysis.retransmission等；而连接符and|or则用来连接各个过滤表达式；每个原词形式的过滤表达式则会包括任意长度的字符串以及一对或多对括号。

下表所列为显示过滤表达式中条件操作符的用途。

![6-2.1-1](https://dn-anything-about-doc.qbox.me/userid2418labid909time1429420525828?watermark/1/image/aHR0cDovL3N5bC1zdGF0aWMucWluaXVkbi5jb20vaW1nL3dhdGVybWFyay5wbmc=/dissolve/60/gravity/SouthEast/dx/0/dy/10)

在参数和条件操作符之间可以不留空格，也可以保留空格。

在过滤表达式中用条件操作符“!=”为eth.addr、ip.addr、tcp.port或udp.port等参数设定条件时，Wireshark总会为其配上黄色背景色，这表示过滤表达式语法无误，但并不会生效，原因如下。

当人们输入类似于ip.addr != 192.168.1.100这样的过滤表达式时，是希望Wireshark过滤掉抓包文件中源和目的IP地址均不为192.168.1.100的数据包。可惜，每个IP数据包必含2个IP地址，一为源IP地址，一为目的IP地址。Wireshark根据上面这条过滤表达式执行显示过滤功能时，只要发现源或目的IP地址至少有一个不为192.168.1.100，便会判定条件为真。出于这个原因，要想让Wireshark显示源和目的IP地址均不为192.168.1.100的数据包，显示过滤表达式的正确写法应该是：!(ip.addr == 192.168.1.100)。

下表所列为显示过滤表达式中逻辑关系操作符的用途。
![6-2.1-2](https://dn-anything-about-doc.qbox.me/userid2418labid909time1429420631822?watermark/1/image/aHR0cDovL3N5bC1zdGF0aWMucWluaXVkbi5jb20vaW1nL3dhdGVybWFyay5wbmc=/dissolve/60/gravity/SouthEast/dx/0/dy/10)


###2.2 配置方法   

可选择下列途径来配置显示过滤器。

借助于显示过滤器窗口

要用此法配置显示过滤器，请把鼠标移至过滤器工具条上的Expression…按钮，如图所示。
 ![6-2.2-1](https://dn-simplecloud.shiyanlou.com/uid/596222/1525746276156.png-wm)


点击此按钮，如图所示的Filter Expression窗口将立刻弹出。

该窗口由5个重要区域构成，如下所列。
>  **Field name**（协议头部中的字段名称）区域：在该区域，可利用Wireshark预定义的协议模板来配置显示过滤器所含各参数。点最左边的“+”号，即可浏览到相关协议的各个属性（或协议头部中各字段的名称），并可选择相应的属性作为显示过滤器的参数。

试举一例，要想基于某一具体的IPv4地址来构造显示过滤器，就得先找到IPv4协议，点其左边的“+”号，便会暴露出Wireshark所支持的IPv4的各项属性（或IPv4包头中的各个字段），然后再选择ip.addr作为显示过滤器的参数即可。
![6-2.2-2](https://dn-simplecloud.shiyanlou.com/uid/596222/1525746348258.png-wm) 


再举一例，要想基于某个具体的TCP源或目的端口号来构造显示过滤器，需先找到TCP协议，点击左边的“+”号，将会暴露出Wireshark所支持的TCP的各项属性（或TCP头部的各个字段），然后再选择tcp.port作为显示过滤器的参数即可。
>  Relation（关系）区域：可从该区域选择条件操作符。选择“==”表示“等于”，选择“！=”表示“不等于”，依次类推。

试举一例，要想让Wireshark只显示包含SIP INVITE方法的数据包，需先在Field name找到SIP协议，点其左边的“+”号，在暴露出的Wireshark所支持的SIP协议的各项属性中选择sip.Method；然后在Relation区域中选择“==”；最后在Value区域中输入invite。
>  Value（值）区域：可在该区域的输入栏内输入事先从Filed name区域中选择的协议头部字段（或协议属性）的字段值（或属性值）。

试举一例，要想让Wireshark只显示TCP头部中SYN标记位置1的数据包，需先在Field name找到TCP协议，点击左边的“+”号，然后在暴露出的Wireshark所支持的TCP的各项属性（或TCP头部中的各个字段）中选择tcp.flags.syn，最后在Value区域的输入栏中输入1。
>  Predefined values（预定义值或预定义选项）区域：该区域是否有效，取决于定义显示过滤器时，在Field name区域中所选择的协议类型或协议属性。该区域的内容既有可能是布尔值（True或Flase），也有可能是Wireshark为某种协议或某种协议的某项属性预先定义的一系列选项。

试举一例，在Field name区域的TCP协议中，包含了一个名为tcp.option_kind的属性，此属性与TCP头部选项有关（欲知更多与TCP头部选项有关的信息，请阅读本书第9章）。若在配置显示过滤器时，选择了该属性，则只要点选Relation区域内的相关条件操作符，在Predefined values（预定义值）区域内便会出现Wireshark为该属性预先定义的某些选项。
>  Range (offset: length)（范围（偏移：长度））区域：可利用该区域中的输入栏，按照“偏移字节数：过滤器检测长度”的格式，来构造字节偏移型显示过滤器。

在显示过滤器工具条的Filter输入栏内直接输入显示过滤语句
只要掌握了显示过滤器的配置语法，在显示过滤器工具条的Filter输入栏内直接输入显示过滤语句，可谓是一种最为方便的配置显示过滤器的方法。
![6-2.2-3](https://dn-simplecloud.shiyanlou.com/uid/596222/1525746437028.png-wm) 


向Filter输入栏内输入显示过滤语句所包含的字符时，输入栏的背景色可能会呈以下三种颜色之一。
>  绿色：这表示输入的过滤语句正确，可应用于抓包文件。
>
>  红色：这表示输入的过滤语句有误，在应用于抓包文件之前必须修改。
>
>  黄色：只要过滤语句中包含了操作符“!=”，Filter输入栏的背景色就会呈黄色，这并不表示过滤语句有误，只是提醒用户，过滤语句在应用于抓包文件之后，可能不会生效。

在抓包主窗口的数据包结构区域内，将数据包的某个属性值指定为显示过滤器的过滤条件

这是一种定义显示过滤器的快捷方法。可在抓包主窗口中的数据包结构区域内，把数据包的某个属性（特征或协议头部字段值）指定为显示过滤器。为此，请在该区域内选中相关数据包的某个属性，单击右键。在右键菜单中，包含了几个与显示过滤器有关的菜单项，如图所示。
![6-2.2-4](https://dn-anything-about-doc.qbox.me/userid2418labid909time1429420957398?watermark/1/image/aHR0cDovL3N5bC1zdGF0aWMucWluaXVkbi5jb20vaW1nL3dhdGVybWFyay5wbmc=/dissolve/60/gravity/SouthEast/dx/0/dy/10) 


以下是对上图所示右键菜单中各菜单项的介绍。
>-  Apply as Filter（直接作为显示过滤器使用）：只要点选了该菜单项下的各子菜单项，事先选定的数据包的属性将会作为显示过滤器（或其中的一项参数），并同时作用于抓包文件。
>-  Prepare a Filter（作为有待应用的显示过滤器）：只要点选了该菜单项下的各子菜单项，事先选定的数据包的属性将会成为有待应用的显示过滤器（或其中的一项参数）（选定后，需点下Apply按钮才能生效）。
>  以下所列为上述两个右键菜单项中都包含的两个子菜单项。
>-  Selected：将选择选定的字段或参数作为显示过滤参数。
>-  Not Selected：以逻辑非的方式将选定的字段或参数作为显示过滤参数。

现举例对以上两个子菜单项的作用加以说明。若在某个HTTP数据包的hypertext transfer protocol下选中request.method：GET，同时单击右键，并在右键菜单中选择了Apply as Filter菜单项下的Selected子菜单项，则Wireshark将会在显示过滤工具栏的Filter输入栏内自动生成显示过滤表达式http.request.method == GET；若选择了Apply as Filter菜单项下的Not Selected子菜单项，则Wireshark会在显示过滤工具栏的Filter输入栏内自动生成显示过滤表达式!(http.request.method == "GET")。这也正是这两个子菜单项的区别所在。

此外，还可以使用Apply as Filter和Prepare a Filter菜单项所包含的“... and selected”、“... or selected”、“... and not selected”或“or ... or not selected”子菜单项来构造显示过滤表达式。

###2.3 幕后原理   

显示过滤器为Wireshark软件所独有。用Wireshark执行抓包分析任务时，有很多地方都会用到显示过滤器，相关内容会在本书后续章节随文讲解。

在显示过滤工具条的Filter输入栏内输入显示过滤器时，可借助于自动补齐特性，来完成过滤器的构造。比如，若在Filter输入栏内输入tcp.f时，自动补齐特性将会生效，会使Wireshark在该输入栏下自动列出所有以tcp.f打头的显示过滤器参数（即TCP数据包的属性或TCP头部中的字段），如图所示。对于本例，以tcp.f打头的显示过滤器参数是tcp.flag（可利用该参数来引用TCP头部中的各标记位字段值）。
![6-2.3-1](https://dn-anything-about-doc.qbox.me/userid2418labid909time1429421067938?watermark/1/image/aHR0cDovL3N5bC1zdGF0aWMucWluaXVkbi5jb20vaW1nL3dhdGVybWFyay5wbmc=/dissolve/60/gravity/SouthEast/dx/0/dy/10) 


###2.4 拾遗补缺   

本节将介绍几个与Wireshark显示过滤器有关的操作技巧。
**如何获悉显示过滤器所包含的参数**

在Wireshark抓包主窗口的数据包结构区域中，只要选中了任何一种协议头部的某个字段，与该字段相对应的显示过滤参数将会出现在抓包主窗口底部状态栏的左侧，如图所示。
 ![6-2.4-1](https://dn-simplecloud.shiyanlou.com/uid/596222/1525746691436.png-wm)


**如何在数据包列表区域中添加新列**

可在Wireshark抓包主窗口的数据包结构区域中，把数据包的某个属性（或协议头部中的某个字段），作为数据包列表区域中的新列。具体的操作方法是，选中数据包的某个属性（或协议头部中的某个字段），点击右键，在右键菜单中选择Apply as Column列。比方说，可把tcp.window_size_value属性作为数据包列表区域中的新列，以便在抓包时同步观察TCP窗口大小。TCP的性能与窗口大小息息相关，第9章会对此展开深入讨论。

**如何保存经过显示过滤器筛选的数据**

要想把应用了显示过滤器之后所看见的数据包另存为一个抓包文件，请点击File菜单下的Export Specified Packets...菜单项，根据弹出窗口中的提示信息来执行相应的操作，如图所示。
 ![6-2.4-2](https://dn-simplecloud.shiyanlou.com/uid/596222/1525746758991.png-wm)


##三、配置Ethernet、ARP、主机和网络过滤器

本章会介绍如何配置第二层过滤器（基于Ethernet地址或Ethernet帧的某些属性来进行过滤）和第三层过滤器（基于IP地址或某IP数据包的某些属性来进行过滤）。此外，还会讲解如何配置地址解析协议（ARP）过滤器。

###3.1 配置准备   

配置Ethernet显示过滤器的目的，是要让Wireshark只显示相关的第二层以太网帧；配置IP显示过滤器的目的，则是让Wireshark只显示必要的第三层IP数据包。第一种过滤器所依据的是MAC地址或Ethernet帧的某些属性，第二种过滤器则要仰仗IP地址或IP数据包的某些属性。

以下两个显示过滤参数经常会在以“帧间间隔时间”为条件来行使过滤功能的显示过滤器中使用。
>-  frame.time_delta：该参数是指当前帧与Wireshark所抓上一帧之间的（接收或抓取）时间间隔，即Wireshark在抓到了上一帧之后隔了多久，收到了当前帧。本书第5章会介绍其用法。
>-  rame.time_delta_displayed：该参数是指当前帧与Wireshark显示出的上一帧之间的（抓取或接收）时间间隔，即Wireshark抓到了已显示出的上一帧（已抓到但未予显示的帧不算）后隔了多久，收到了当前帧。该参数的用法也将在本书第5章介绍。
>  通过分析Wireshark所抓数据帧之间的时间间隔，可对解决TCP性能问题起到很大的帮助。可在Wireshark IO Graphs中利用以上两个参数，来监控TCP的性能。

以下所列为实战中常用的L2（Ethernet）显示过滤器。
>  eth.addr == <MAC Address>：让Wireshark只显示具有指定MAC地址的数据帧。
>
>  eth.src == <MAC Address>：让Wireshark只显示具有指定源MAC地址的数据帧。
>
>  eth.dst == <MAC Address>：让Wireshark只显示具有指定目的MAC地址的数据帧。
>
>  eth.type == <Protocol Type（十六进制数，格式为0xNNNN）>：让Wireshark只显示指定以太网类型的流量。

以下所列为实战中常用的ARP显示过滤器。
>  arp.opcode == <value>：让Wireshark只显示指定类型的ARP帧（ARP帧按其所含操作代码字段值，可分为：ARP应答帧、ARP响应帧、RARP应答帧、RARP响应帧） 。
>
>  arp.src.hw_mac == <MAC Address>：让Wireshark只显示由指定MAC地址的主机发出的ARP帧 。

以下所列为实战中常用的IP显示过滤器。
>  ip.addr == <IP Address>：让Wireshark只显示发往或源自设有指定IP地址的主机的数据包。
>
>  ip.src == <IP Address>：让Wireshark只显示由设有指定IP地址的主机发出的数据包。
>
>  ip.dst == <IP Address>：让Wireshark只显示发往设有指定IP地址的主机的数据包。
>
>  ip.ttl == <value>、ip.ttl < value>或ip.ttl > <value>：让Wireshark只显示IP包头中TTL字段值为指定值的数据包。
>
>  ip.len = <value>、ip.len > <value>或ip.len < <value>：让Wireshark只显示指定长度的IP数据包（IP包头中有一个2字节的总长度字段）。
>
>  ip.version == <4/6>：让Wireshark只显示具有指定IP版本号的IP数据包（IP包头，不论IPv4还是IPv6，都包含了一个1字节的版本号字段）。

###3.2 配置方法

下表所列为若干常用L2和L3 Wireshark显示过滤器的举例。
![6-3.2-1](https://dn-anything-about-doc.qbox.me/userid2418labid909time1429421393247?watermark/1/image/aHR0cDovL3N5bC1zdGF0aWMucWluaXVkbi5jb20vaW1nL3dhdGVybWFyay5wbmc=/dissolve/60/gravity/SouthEast/dx/0/dy/10)

续表
![6-3.2-2](https://dn-anything-about-doc.qbox.me/userid2418labid909time1429421375744?watermark/1/image/aHR0cDovL3N5bC1zdGF0aWMucWluaXVkbi5jb20vaW1nL3dhdGVybWFyay5wbmc=/dissolve/60/gravity/SouthEast/dx/0/dy/10)

上表给出了IPv4和IPv6地址与显示过滤器参数ip.addr和ipv6.addr配搭使用时的表示方法。只要Wireshark显示过滤器语句中包含有IPv4或IPv6地址，都可以采用与上表相同的表示方法。

**Ethernet过滤器**

Ethernet过滤器分为以下两类。
>-  要让Wireshark只显示发往或源于具有某MAC地址的主机的数据帧，显示过滤器的写法应类似于eth.src == 10:0b:a9:33:64:18或eth.dst ==10:0b:a9:33:64:18。
>-  要让Wireshark只显示以太网广播帧，显示过滤器的写法为：Eth.dst == ffff.ffff.ffff。

**ARP过滤器**

ARP过滤器也分以下两类。
>-  要让Wireshark只显示ARP请求帧，显示过滤器的写法为arp.opcode == 1。
>-  要让Wireshark只显示ARP应答帧，显示过滤器的写法为arp.opcode == 2 。

**IP和ICMP过滤器**

>-  要让Wireshark只显示由设有指定IP地址的主机发出的IP数据包，显示过滤器的写法应类似于ip.src == 10.1.1.254。
>-  要让Wireshark只显示所有IP数据包，但设有某指定IP地址的主机发出的IP数据包除外，显示过滤器的写法应类似于!ip.src == 64.23.1.1。
>-  要让Wireshark只显示交换于某一对IP主机之间的所有IP数据包，显示过滤器的写法应类似于ip.addr == 200.1.1.1 and ip.addr ==192.168.1.1。
>-  要让Wireshark只显示发往IP多播目的地址的所有数据包，显示过滤器的写法应类似于ip.dst == 224.0.0.0/4。
>-  要让Wireshark只显示发源于IP子网192.168.1.0/24的所有IP数据包，显示过滤器的写法为ip.src==192.168.1.0/24。
>-  要让Wireshark只显示发往或源于设有某个（或某些）IPv6地址的主机的IPv6数据包，显示过滤器的写法应类似于：
>-  ipv6.addr == ::1
>-  ipv6.addr == 2008:0:130F:0:0:09d0:666A:13ab
>-  ipv6.addr == 2006:0:130f::9c2:876a:130b
>-  ipv6.addr == ::

**复杂的过滤器**

>-  要让Wireshark只显示由隶属于指定IP子网（比如10.0.0.0/24）的主机，发往域名中包含指定字符串的网站（比如“sohu”）的所有IP流量，显示过滤器的写法为：ip.src == 10.0.0.0/24 and http.host contains "sohu"。
>-  要让Wireshark只显示由隶属于指定IP子网（比如10.0.0.0/24）的主机，访问域名以.com结尾的网站的所有IP流量，显示过滤器的写法为：ip.addr == 10.0.0.0/24 or http.host matches "\.com$"。
>-  要让Wireshark只显示发源于指定IP子网（比如10.0.0.0/24）的所有IP广播流量，显示过滤器的写法为：ip.src ==10.0.0.0/24 and eth.dst == ffff.ffff.ffff。
>-  要让Wireshark只显示所有广播包，但主机在执行ARP请求操作时所触发的广播包除外，显示过滤器的写法为：not arp and eth.dst == ffff.ffff.ffff。
>-  要让Wireshark显示除ICMP包和ARP帧以外的所有流量，显示过滤器的写法为：not arp && not icmp或not arp and not icmp 。


###3.3 幕后原理

本节是对上一节所举显示过滤器示例的幕后原理的解释。

**以太网广播**

以太网广播帧是指目的MAC地址为全1的以太网帧，正因如此，要让Wireshark只显示已抓取的所有以太网广播帧，显示过滤器应写成：eth.dst == ffff.ffff.ffff（十六进制数F等于二进制数1111）。

**IPv4多播**

IPv4多播数据包的目的IP地址范围介于224.0.0.0～239.255.255.255之间，若转换为二进制，则介于
11100000.00000000.00000000.0000000～11101111.11111111.11111111.11111111之间。
仔细观察IPv4多播地址的二进制表示方式，应不难发现，IPv4多播地址一定是以1110打头。因此，要让Wireshark只显示已抓取的所有IPv4多播数据包，显示过滤器应写成：ip.dst == 224.0.0.0/4。也就是说，首字节的头4位为1110（11100000，224），掩码长度为4位的IP地址都属于IPv4多播地址范围，此类IPv4地址的首字节总是介于224～239之间。

**IPv6多播**

IPv6多播地址的首字节总是ff，随后的一个字节由4位标记字段和4位范围字段组成。因此，要筛选出IPv6多播数据包，显示过滤器就应该写成ipv6.dst == ff00::/8。ff00::/8表示以ff打头的所有IPv6地址，即IPv6多播地址。

###3.4 进阶阅读

>  欲知更多有关以太网（LAN）的内容，请参阅本书第7章。

版权声明
>
> Copyright © Packt Publishing 2013. First published in the English language under the title Network Analysis Using Wireshark Cookbook.
>
> All Rights Reserved.
>
> 本书由英国Packt Publishing公司授权人民邮电出版社出版。未经出版者书面许可，对本书的任何部分不得以任何方式或任何手段复制和传播。
>
> 版权所有，侵权必究。
