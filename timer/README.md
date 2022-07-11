
定时器处理非活动连接
===============
由于非活跃连接占用了连接资源，严重影响服务器的性能，通过实现一个服务器定时器，处理这种非活跃连接，释放连接资源。利用alarm函数周期性地触发SIGALRM信号,该信号的信号处理函数利用管道通知主循环执行定时器链表上的定时任务.若该段时间内没有交换数据，则将该连接关闭，释放所占用的资源。
> * 统一事件源
> * 基于升序链表的定时器
> * 处理非活动连接


统一事件源
信号处理函数使用管道将信号传递给主循环，信号处理函数往管道的写端写入信号值，主循环则从管道的读端读出信号值，使用 I/O 复用系统调用来监听管道读端的可读事件，这样信号事件与其他文件描述符都可以通过 epoll 来检测，从而实现统一处理

定时器处理只在主线程中进行处理，每次处理都有先后顺序，不会被竞争所以不用加锁