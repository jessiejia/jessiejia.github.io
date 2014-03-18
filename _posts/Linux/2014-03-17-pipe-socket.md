---
layout: post
title: 进程通信
category: 运维 
tags: pipe socket
---

#### 管道 pipe (通过内核缓冲区交换数据) [教程](http://learn.akae.cn/media/ch30s04.html)
> 每个进程各自有不同的用户地址空间，任何一个进程的全局变量在另一个进程中都看不到，所以进程之间要交换数据必须通过内核，在内核中开辟一块缓冲区，进程1把数据从用户空间拷到内核缓冲区，进程2再从内核缓冲区把数据读走，内核提供的这种机制称为进程间通信（IPC，InterProcess Communication）
> 文件描述符
![内核缓冲区](/public/upload/pipe1.png)
![pipe](/public/upload/pipe2.png)


#### socket
* 使用IP及端口传送数据
* 异步全双工
{%raw%}
```
<?php  
// 建立server端socket  
$tcp = getprotobyname("tcp");  
$socket = socket_create(AF_INET, SOCK_STREAM, $tcp);  
socket_bind($socket, '127.0.0.1', 10008);       //绑定要监听的端口  
socket_listen($socket);       //监听端口  
  
//初始化一个数据，和客户端通信  
$buffer = "connect";  
while (true) {  
    // 接受一个socket连接  
    $connection = socket_accept($socket);  
    if(!$connection){  
        echo "connect fail";  
    }else{  
            echo "Socket connected\n";  
            // 向客户端传递一个信息数据  
            if ($buffer != "") {  
                echo "send data to client\n";  
                socket_write($connection, $buffer . "\n");  
                echo "Wrote to socket\n";  
            } else {  
                echo "no data in the buffer\n" ;  
            }  
            // 从客户端取得信息  
            while ($data = @socket_read($connection, 1024, PHP_NORMAL_READ)) {  
                    printf("Buffer: " . $data . "\n");  
                    //取得信息给客户端一个反馈  
                    socket_write($connection, "Information Received\n");  
            }  
    }  
  
    socket_close($connection);  
    //关闭 socket  
    printf("Closed the socket\n");  
}  
```

```
<?php  
// 建立客户端的socet连接  
$socket = socket_create(AF_INET, SOCK_STREAM, SOL_TCP);  
$connection = socket_connect($socket, '127.0.0.1', 10008);    //连接服务器端socket  
  
while ($buffer = @socket_read($socket, 1024, PHP_NORMAL_READ)) {  
    //服务端告诉客户端，自己的状态  
    if (preg_match("/not connect/",$buffer)) {  
        echo "don`t connect\n";  
        break;  
    } else {  
        //服务器传来信息  
        echo "Buffer Data: " . $buffer . "\n";  
  
        echo "Writing to Socket\n";  
        // 将客户的信息写到通道中，传给服务器端  
        if (!socket_write($socket, "SOME DATA\n")) {  
            echo "Write failed\n";  
        }  
        //服务器端收到信息后，给于的回应信息  
        while ($buffer = socket_read($socket, 1024, PHP_NORMAL_READ)) {  
                echo "sent to server: SOME DATA\n response from server was:" . $buffer . "\n";  
        }         
  
    }  
}
```
{%endraw%}

