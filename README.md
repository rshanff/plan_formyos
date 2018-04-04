# plan_formyos


构建根文件系统可以使用buildroot

基础通用组件：

1.进程间通信组件
     1.1
     借鉴内核linux/netlink.h中的消息组装方式(TLV)，实现自己的TLV用于进程间通信，命令行配置信息。

      openwrt的libubox中blobmsg可以很好的满足需求。

      openwrt的libubox下载地址：
      http://git.openwrt.org/project/libubox.git

     1.2
     消息队列
     msg
     
     freebsd中有一个kqueue的实现机制，可以参考它的实现来，搞一个用户态和内核态都能使用的queue消息队列。
     
     
2. 定时操作

    参考内核的定时实现机制，实现用户态的定时操作。
    linux/timer.h  kernel/timer.c 
    
 
3. 信号操作

    
    2.6之后的某个版本,内核提供了把时间 信号转换成文件描述符的接口,所谓一切皆文件嘛！
    
    这么做的好处就是可以把定时器和信号这种异步实现机制加入到事件处理机制中，把他们当成事件来处理。
    
4. 基础的数据结构
   
   把内核的list hlist list_nulls  list 给移植到用户态
   
   
5. 事件机制 
   
   原生态的linux epoll 没有回调函数的机制，这样当事件来的时候需要根据fd来判断自己关注的事件，然后再调用相应的事件处理函数。现在有很多用户态的实现都是
   增加一个函数的callback机制，这样处理可以是用户态开发代码清晰、易用、提高性能。比如libevent、openwrt的libubox的uloop实现，当然它们增加了相应的就
   绪队列。其实epoll的内核实现就已经有事件队列和就绪队列两个队列了，没必要再在用户态实现一次。
   
   改造epoll的实现，增加一个事件callback，当事件发生时直接调用callback。
   
   对于内核态socket编程，内核现在已经提供有和用户态对应的一系列函数，函数名kernel_xxx在net/socket.c中通过EXPORT_SYMBOL导出,在linux/net.h中声明。
   既然socket BSD接口都在内核态提供了，是否可以实现一个kernel_epoll在内核态使用呢？一定有办法可以实现的。
   
6. 进程管理

    linux的进程管理并不能当一个进程挂掉之后给它再次重启，需要改造init进程，使进程挂了之后可以自动重启。一般的做法是创建一个子进程作为工作进程，父进程
    作为守护进程，当工作进程挂掉之后，守护进程负责收尸和重启工作进程。这种做法不仅代码复杂，而且最重要的是增加内存消耗。多占用一半系统资源。
    
    openwrt的procd实现了不做的进程管理方式，但是它并没有实现自动重启进程的功能。可以参考它的实现来改造linux的init进程，使其实现自动重启进程的功能。

7.  参考freebsd和linux的编译框架，搭建自己的编译框架 


荔枝派Zero
https://www.kancloud.cn/lichee/lpi0/317714
