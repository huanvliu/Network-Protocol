#### ifconfig 和 ipconfig,熟悉又陌生的命令

&nbsp;&nbsp;&nbsp;&nbsp;ifconfig是Linux下查询IP的命令，ipconfig是windows下查询IP的命令，我相信这两个命令大多数人是经常使用的。因为我们服务器一般使用
的是Linux，这里就用Linux为例子。在Linux下，你知道还有其它命令可选查询IP地址么？答案是肯定的：ip addr。那ifconfig和ip addr有什么区别呢？这是net-tools
与iproute2之间的故事，感兴趣的可以去百度一下，了解这个有趣的知识点和背景。有些时候，我们会使用删减版的Linux，很可能就没有这两个命令，那么就需要我们自己安
装了。当然，一般情况下，这两个命令都会是系统自带的。

&nbsp;&nbsp;&nbsp;&nbsp;我们运行一下ip addr，你会看到如下的内容：
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:cc:b2:a2 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
       valid_lft 74491sec preferred_lft 74491sec
    inet6 fe80::eaac:2dc:3801:d17e/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```
这个命令显示了这台机器上所有的网卡，大部分的网卡都会有一个IP地址，当然，IP并不是必须的，后面会说到没有IP的情况。上节说过，IP地址是一个网卡在网络世界中通讯的
地址，相当于我们显示世界中的门牌号。既然是门牌号，那肯定不能一样，不然就会冲突，电脑有时候会弹出网络地址冲突，然后上不了网，这就是是IP冲突的情况。

&nbsp;&nbsp;&nbsp;&nbsp;看上面的输出结果，10.0.2.15 就是IP地址，这个地址被点号分成4部分，每部分都有8bit，所以IP地址一共32位，但32位的IP很快就不够用了，
因为当时设计的时候，并没有现在这么多的计算机。所以下一代的IPv6也因运而生，也就是inet6这一段：fe80::eaac:2dc:3801:d17e/64 ，IPv6有128位，现在看来是够了，
但以后的事谁说得准。但现在IPv4的32位地址还是最通用的，我们仔细学习一下32位的IP地址划分。

&nbsp;&nbsp;&nbsp;&nbsp;32位地址本来数量就不是很多，但还被分为了A,B,C,D,E五大类。D,E两类被用于特殊用途，我们主要讲解民用的A，B，C三类，这3类地址都分为
两部分，前面一部分是网络号，后面一部分是主机号。这很好理解，类似于小区A的六单元10001和小区B的六单元10001,小区A，B就是网络号，六单元10001就是主机号。

表格

可以看出，A类可支持16777214台主机，B类支持65534台主机，C类只支持254台。C类可支持的主机数量太少，现在已经网吧都可能不止这么点电脑，但A，B数量又过于庞大，
一般的企业都达不到6万多台电脑，但没用的IP地址就是浪费资源。

&nbsp;&nbsp;&nbsp;&nbsp;鉴于A,B,C的分类不太合理，现在有了一个折中的方案叫做无类型域间选路，简称CIDR。打破传统的5大类划分方式，任然讲32位置一分为二
位网络号和主机号。但看前面的输出地址：10.0.2.15/24，后面带了个“/24”，这种地址的表达方式就是CIDR，代表钱24位是网络号，那么剩下的8位就是主机号。伴随着CIDR
存在的，一个是广播地址：10.0.2.255，如果发送消息到这个地址，那么10.0.2网络中的地址都会收到消息。另一个就是子网掩码：255.255.255.0.

&nbsp;&nbsp;&nbsp;&nbsp;将子网掩码与IP地址进行与运输，前面3个255，转成二进制都是1，运算后都是原来的数值，因而前3位不变，还是10.0.2。后面一个是0，与了之后
都是0，所以合起来网络号就是：10.0.2.0。所以IP地址和子网掩码进行与运算，就会得到网络号。CIDR网络号的计算一定要注意网络号的位数，最好全转成二进制在预算，最后在
转成10进制。在五大分类中D类是组播地址，使用这一类地主，属于某个组的机器都能收到，类似于邮件组的概念，后面讲到VXLAN协议的时候会提到。上述就是IP的分类
和相关的一些知识点。

&nbsp;&nbsp;&nbsp;&nbsp;在日常生活中，我们很少去划分A,B,C类，一般记得CIDR就不错了。但还有一点就是IP分为公有IP和私有IP地址。平时我们在家，在办公室，在学校
看到的IP都是私有IP，是网络管理员自己划分的，子不同的局域网内是可以重复的。但当我们想要开发一个网站，一个网络服务给全世界使用的时候，我们就需要去申请
一个公有的IP，这个一般需要去购买，比如国内的阿里云，腾讯等。

&nbsp;&nbsp;&nbsp;&nbsp;上面的才只讲了关于IP地址的一些信息，ip addr的输出更丰富的信息。
