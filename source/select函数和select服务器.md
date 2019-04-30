---
title: select函数和select服务器
categories: 网络编程
date: 2018-03-28 00:00:00
tags:
- 网络编程
- select函数
---
# **I/O**模型

Linux把所有的外部设备都看做文件，所以，对设备的操作，机等同于对文件的操作，每打开一个文件，就会返回一个对应的文件描述符。

每一个文件描述符I/O操作，通常是这样

1.等待数据准备好

2.从内核空间复制数据到进程空间

根据处理的过程，将I/O分为一下几种模型

- 阻塞式I/O
- 非阻塞式I/O
- I/O复用(select&poll属于这种方式)
- 信号驱动I/O
- 异步I/O

**1.阻塞式I/O**

在等待数据准备好的时候，进程一直等待，直到数据准备好，将数据复制到进程缓冲区，然后，进程开始处理。

**2.非阻塞式I/O**

当数据没有准备好的时候，内核会给进程返回一个错误，而进程不会阻塞。

**3.I/O复用**

select&poll可以同时监控多个文件描述符(一般为1024个)，每当有一个数据准备好的时候，select就会返回，接着，就可以从缓冲区中，将数据拿出来处理。其特点是，单线程的情况下，可以处理多个I/O。

**4.信号驱动I/O**

通过系统调用 `sigaction` 执行一个信号处理函数（非阻塞，立即返回）。当数据就绪时，会为该进程生成一个 SIGIO 信号，通过信号回调通知应用程序读取数据。

**5.异步I/O**

告知内核启动某个事件，并让内核在整个操作完成后（包括将数据从内核复制到用户自己的缓冲区）通知我们。

# select()作用和操作

select()原型

```C++
int select(int maxfdp, fd_set *readset, fd_set *writeset, fd_set *exceptset,struct timeval *timeout);
```

**1.maxfdp:**

最大文件描述符+1。Linux描述符从0开始。`0：stdio`，`1：stdout`,`2:stderr`,所以，其他文件描述符从3开始，对于select来说，其能最大监视的文件描述符大小为***1024***。

因为，select是对所有监视的文件描述符进行扫描，如果有描述符（设备）就绪，则置位。那么，就好理解了，select监视最大1024个，也就是0-1023，则，将值设置为1024，就是最大的。

**2.readset, writeset, excptset**

分别为，可读、可写、异常的集合。select会去检查集合中的值，如果有设备就绪（读，写，异常），则会将对应的值置位，然后，select返回，唤醒进程。后续就可以去检查集合中的值有没有被置位。

**3.timeout**

告诉内核，返回的时候，等待任何一个描述符就绪花多长时间。

1. NULL。永远等待，直到有一个描述符就绪的时候。
2. 常数。即等待这样一个时间后，不管有没有描述符就绪，都返回，
3. 0。不等待，检查完后，立即返回。

## 对set的操作

1. FD_ZERO(&fd_set)，将所有位清0。
2. FD_SET(fd,&fdset)，将某一位置位1.
3. FD_CLI(fd,&fd_set)，将某一位置0。
4. FD_ISSET(fd,&fd_set)，判断某个描述符是否被置位1，即是否有数据准备好。

## select的调用过程

```
（1）使用copy_from_user从用户空间拷贝fd_set到内核空间
（2）注册回调函数__pollwait
（3）遍历所有fd，调用其对应的poll方法（对于socket，这个poll方法是sock_poll，sock_poll根据情况会调用到		tcp_poll,udp_poll或者datagram_poll）
（4）以tcp_poll为例，其核心实现就是__pollwait，也就是上面注册的回调函数。
（5）__pollwait的主要工作就是把current（当前进程）挂到设备的等待队列中，不同的设备有不同的等待队列，对于		tcp_poll来说，其等待队列是sk->sk_sleep（注意把进程挂到等待队列中并不代表进程已经睡眠了）。在设备收到一条消	   息（网络设备）或填写完文件数据（磁盘设备）后，会唤醒设备等待队列上睡眠的进程，这时current便被唤醒了。
（6）poll方法返回时会返回一个描述读写操作是否就绪的mask掩码，根据这个mask掩码给fd_set赋值。
（7）如果遍历完所有的fd，还没有返回一个可读写的mask掩码，则会调用schedule_timeout时调用select的进程（也就是	current）进入睡眠。当设备驱动发生自身资源可读写后，会唤醒其等待队列上睡眠的进程。如果超过一定的超时时间	      （schedule_timeout指定），还是没人唤醒，则调用select的进程会重新被唤醒获得CPU，进而重新遍历fd，判断有没有     就绪的fd。
（8）把fd_set从内核空间拷贝到用户空间。

```

select缺点

```
（1）每次调用select，都需要把fd集合从用户态拷贝到内核态，这个开销在fd很多时会很大
（2）同时每次调用select都需要在内核遍历传递进来的所有fd，这个开销在fd很多时也很大
（3）select支持的文件描述符数量太小了，默认是1024
```
<https://juejin.im/post/59f9c6d66fb9a0450e75713f#heading-0>

# select版服务器

```C++
#include <sys/types.h>
#include <sys/socket.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/time.h>
#include <sys/ioctl.h>
#include <unistd.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <iostream>
#include <vector>
#include <algorithm>

#define PORT 10001
#define RT_ERR (-1)
#define RT_OK 0
#define SERVERIP "127.0.0.1"
#define LISTEN_QUEUE 10
#define BUFFER_SIZE 1024
int test()
{
    std::vector<int>v;
    int listenfd, connsockfd;
    char readbuf[BUFFER_SIZE];
    listenfd = socket(AF_INET, SOCK_STREAM, 0);
    if(listenfd < 0)
    {
            fprintf(stderr, "socket function failed.\n");
            exit(RT_ERR);
    }

    struct  sockaddr_in serveraddr, clientaddr;
    bzero(&serveraddr, sizeof(serveraddr));
    serveraddr.sin_family = AF_INET;
    serveraddr.sin_port = htons(PORT);
    serveraddr.sin_addr.s_addr = inet_addr(SERVERIP);

    unsigned int client_len = sizeof(struct sockaddr_in);
    if(bind(listenfd, (struct sockaddr *)&serveraddr, sizeof(struct sockaddr_in)) < 0)
    {
            fprintf(stderr, "bind function failed.\n");
            close(listenfd);
            exit(RT_ERR);
    }

    if(listen(listenfd,LISTEN_QUEUE) < 0)
    {
            fprintf(stderr, "listen function failed.\n");
            close(listenfd);
            exit(RT_ERR);
    }
    fprintf(stdout, "The server IP is %s, listen on port: %d\n",inet_ntoa(serveraddr.sin_addr), ntohs(serveraddr.sin_port));

    fd_set readfdset;//可读集合
    while(1)
    {
        FD_ZERO(&readfdset);//将集合所有位清空
        for(auto fd:v)//将每个客户端都置位1
        {
            //为什么要这么做
            //当有客户端加入的时候，如果立即FD_SET的话，就意味着，他已经有数据了，但是，万一没有，逻辑错误。
            FD_SET(fd, &readfdset);
        }
        FD_SET(listenfd, &readfdset);//将监听套接字置位1
/*
对于select函数，传入的参数，在检查的时候，会进行修改，所以，当select返回的时候，readset就存的是，可以读的集合
select检查的时候，只会去检查那些被置位1的套接字，如果不可读/写，则置位0
所以，每一次循环的时候，都得将set中，我们希望检测的位置1。
*/
        if(!(select(FD_SETSIZE, &readfdset, NULL, NULL, NULL) > 0))
        {
            fprintf(stderr, "select function failed.\n");
            close(listenfd);
            exit(RT_ERR);
        }
        if(FD_ISSET(listenfd, &readfdset))
        {
//            fprintf(stdout, "fd is %d, listenfd is %d\n", fd, listenfd);
            std::cout<<"new client"<<std::endl;
            if((connsockfd = accept(listenfd, (struct sockaddr*)&clientaddr, &client_len)) < 0)
            {
                fprintf(stderr, "accept function failed.\n");
                exit(RT_ERR);
            }
            v.push_back(connsockfd);//将客户端加入数组中，遍历时，不需要从0-1024遍历
            fprintf(stdout, "It is a new session from IP:%sport:%d\n",inet_ntoa(clientaddr.sin_addr), ntohs(clientaddr.sin_port));
        }
         for(int fd=listenfd+1;fd<FD_SETSIZE;fd++)
         {
             if(FD_ISSET(fd, &readfdset))
             {
                 if(recv(fd, readbuf, BUFFER_SIZE, 0) > 0)
                 {
                     fprintf(stdout, "recv message: %s\n", readbuf);
                 }
                 else
                 {
                     FD_CLR(fd,&readfdset);
                     auto t=std::find(v.begin(),v.end(),fd);
                     v.erase(t);
                     close(fd);
                     fprintf(stdout, "client socket %d close\n", fd);
                 }
             }
        }//end of for
    }//end of while
    close(listenfd);
    return 0;
}
```

# 客户端

```C++

#include <sys/types.h>
#include <sys/socket.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/time.h>
#include <sys/ioctl.h>
#include <unistd.h>
#include <netinet/in.h>
#include <arpa/inet.h>

#define SERVERIP "127.0.0.1"
#define PORT 10001
#define BUFFER_SIZE 512

int test()
{
    int sockfd;
    struct sockaddr_in server;
    char sendbuf[BUFFER_SIZE];
    bzero(&sendbuf,sizeof(sendbuf));
    sockfd = socket(AF_INET,SOCK_STREAM,0);
    bzero(&server,sizeof(server));
    server.sin_family = AF_INET;
    server.sin_port = htons(PORT);
    server.sin_addr.s_addr = inet_addr(SERVERIP);


    if(connect(sockfd,(struct sockaddr *)&server,sizeof(struct sockaddr)) < 0)
    {
            perror("connect failed.\n");
            return -1;
    }
    while(1)
    {
        fgets(sendbuf, sizeof(sendbuf), stdin);
        send(sockfd, sendbuf, sizeof(sendbuf), 0);
        bzero(&sendbuf,sizeof(sendbuf));
    }
    close(sockfd);
    return 0;
}


```

**参考**

[https://my.oschina.net/fileoptions/blog/911091#h1_2](https://my.oschina.net/fileoptions/blog/911091#h1_2)

[http://www.cnblogs.com/apprentice89/archive/2013/05/09/3070051.html](<http://www.cnblogs.com/apprentice89/archive/2013/05/09/3070051.html>)

[https://juejin.im/post/59f9c6d66fb9a0450e75713f#heading-0](<https://juejin.im/post/59f9c6d66fb9a0450e75713f#heading-0>)

