---
layout: post
title: "select poll epoll"
description: ""
category: grass

---
{% include JB/setup %}

###名词解析
+ mmap：内存映射。具体的意思就是共享同一块内存进行消息传递。我看见到比较多的地方就是内核态和用户态之间
的消息传递采用mmap技术。很明显，如果是使用一般的复制方法的话，在内核态和用户态之间的消息传递所需要的开销
就会很大。而使用了共享一块内存的技术之后就可以大大提高消息传递的效率。


###总述
它们的作用都是监听文件描述符，姑且可以这么认为，然当主要作用是它监听文件描述符所发挥的作用了。

###select和poll
这两个比较相似，其中select要早于poll出现。select默认监听文件描述符为1024个，在sys/select.h中有定义，
虽然可以修改值的大小,但是改变大小之后对使用的性能会有一定的影响。select和poll都使用了轮询的方法。
，这就意味着，当单个进程打开的文件描述符比较多的时候，就会出现一个相对比较长的轮询周期，效率会大大降低。
并且，在内核态向用户太传递消息的时候，使用复制的方式，向用户态进程发送消息，这是一种水平触发的方式，
即，当某个文件描述符处于就绪状态的时候，内核态就会向用户态进程发送消息。而当用户态进程没有处理的时候。
下一轮轮询的时候，还会继续向用户太进程发送消息。poll相对select不同的地方，poll中对文件描述符采用链表组织，
因此访问的大小没有限制。

###epoll
这个方法是在2.6内核中才加进去的。相对于select和poll，有两点比较好的提升：1，不再是采用轮询方式，而是基于
时间就绪通知方式，内核采用一种类似回调的机制，对就绪文件描述符发出通知，支持水平触发和边缘触发。2，
采用了mmap技术，用户态和内核态共享一块内存区域，这样就避免了两个态之间频繁的数据复制。

