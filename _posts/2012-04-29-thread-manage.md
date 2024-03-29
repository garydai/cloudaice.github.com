---
layout: post
title: "linux下的进程调度方法小记"
description: ""
category: linux 

---
{% include JB/setup %}

####linux进程调度策略小看

之前在操作系统课程中学过关于进程调度的算法，linux基本上是基于优先级的时间片轮转法。
目的就是既能够实现进程之间的抢占，又不至于让低优先级的进程被饿死。具体策略很多，记得
0.11内核就是根据时间片来动态修改进程优先级实现的。在0.11中就绪进程队列是用链表实现的。
因此理论上要找到最高优先级的进程，时间开销应该是0（N）。后来我以为现在linux内核对就绪队列
的管理是采用优先队列的，傻傻的想，时间开销应该是O（logN）。之前一次面试的时候，面试官问我
2.6内核中的进程调度算法是怎样实现的。我当时用0.11的思想讲了一下，然后提了优先队列的思想。
面试官很得意的说2.6内核的进程调度是O（1）的。并且还说让我回去好好看看。于是在网上搜寻了一
些资料。大自思想了解了一些。其中O（1）指的就是linux中的进程优先级个数是一个常熟。具体实现方法如下：
从《深入理解linux内核》可以了解到，linux基本上将优先级分成100-139。可见这里一共也就是40个大小的优先级。
linux在进程队列中使用bitmap设计了一个队列。分别用1和0表示该优先级的进程队列是否为空，如果不为空，则为1，否则为0
，因此每次只要从最高优先级处开始扫描，知道找到标记为1的地方。然后就可以找到该优先级的进程列表，进行调度。也就是实现了
调度开销为O（1）的算法。的确很巧妙。另外在看资料的时候还看到一种Cgroup的方法，基本上是一种分类的方法。“容器嵌套容器”。
在很多地方，算法优化都会采用到“分类”的思想方法。包括hash，B树，二分等等（个人观点）。
