# TCP/IP 网络编程

```c
int main (int argc, char * argv[])
//argc 是argument count的缩写表示传入main函数中的参数个数，包括这个程序本身
//argv 是 argument vector的缩写表示传入main函数中的参数列表，其中argv[0]表示这个程序的名字
```

在本项目里argc=3

```c
argv[0] = ./Tcp_Client
argv[1] = 81.69.173.188
argv[2] = 9190
//这个表示我们输入进去的参数，第一个是函数名，第二个表示为IP地址，第三个表示为端口
```

# Chp1

## 1.1

套接字：用来连接该网络的工具

### socket()  bind() listen() accept()

`socket()` 创建套接字，安装电话机

```
#include <sys/socket.h>
int socket(int domain, int type, int protocol);
//成功时返回文件描述符，失败返回-1
```

`bind()` 给套接字分配地址，分配电话号码

```
#include <sys/socket.h>
int bind(int sockfd, struct sockaddr *myaddr, socklen_t addrlen);
//成功时返回0，失败时返回-1
```

`listen()` 转为可接受请求状态，连接电话线

```c
#include <sys/socket.h>
int listen(int sockfd, int backlog);
// sock 希望进入等待连接请求状态的套接字文件描述符
// backlog, 连接请求等待队列的长度，若为5，则队列长度为5，表示最多使5个连接请求进入队列
// 成功返回0，失败返回-1
```

`accept()` 受理连接请求，拿起话筒

```
#include <sys/socket.h>
int accept(int sockfd, struct sockaddr *addr, socklen_t *addrlen);
// 成功返回文件描是符号，失败时返回-1
```

服务器端由以上四个函数构建，客户端调用`socket()`和`connect()` 创建套接字和发送连接请求。

`connect()` 客户端套接字

```
#include <sys/socket.h>
int connect(int sockfd, struct sockaddr *serv_addr, socklen_t addrlen);
//成功返回0，失败返回-1
```

## 1.2 基于Linux的文件操作

### 底层文件访问 low-level file Access 和文件描述符 file Descriptor

分配给标准输入输出及标准错误的文件描述符

| 文件描述符 | 对象                      |
| :--------- | ------------------------- |
| 0          | 标准输入：Standard Input  |
| 1          | 标准输出：Standard Output |
| 2          | 标准错误：Standard Error  |
| -1         | 文件失败                  |

文件和套接字一般经过创建过程才会被分配文件描述符。以上表未经过特殊创建也会自动分配

### 打开文件

```
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
int open(const char *path, int flag)
// path:文件名字符串的地址 flag：文件打开模式信息
```

### 关闭文件

```
#include <unistd.h>
int close(int fd); //成功返回0，失败返回-1
//fd 需要关闭的文件或套接字的文件描述符
```

### 将数据写入文件

```
#include <unistd.h>
ssize_t wte(int fd, const void * buf, size_t nbytes);
//fd 显示数据传输对象的文件描述符，buf 保存要传输数据的缓冲地址值，nbytes 要传输数据的字节数
//成功返回字节数，失败返回-1
```

`size_t` 使通过`typedef` 声明的 `unsigned int`，多加的`s` 相当于 `signed`。`ssize_t`是通过 typedef 声明的 `signed int` 类型。

#### 后缀为`_t` 的数据类型

都是元数据类型（primitive），在`sys/types.h`中由typedef声明定义，就是在不同机器中数据位数定义不统一，减少代码改动。例如`int`有16位和32位，根据系统不同而变化。

### 文件描述符与套接字

下面将同时创建文件和套接字，用整数型态比较返回文件描述符值。

```
#include <stdio.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/socket.h>
int main() {
    int fd1, fd2, fd3;
    fd1 = socket(PF_INET, SOCK_STREAM, 0); //流套接字
    fd2 = open("test.dat", O_CREAT|O_WRONLY|O_TRUNC);
    fd3 = socket(PF_INET, SOCK_DGRAM, 0); //数据报套接字
    printf("file descriptor 1: %d\n", fd1);
    printf("file descriptor 2: %d\n", fd2);
    printf("file descriptor 3: %d\n", fd3);
    close(fd1);
    close(fd2);
    close(fd3);
    return 0;
}
```

#### 文件描述符

文件描述符是计算机科学中的一个术语，表示指向文件的引用的抽象化概念。**形式上是一个非负整数**。实际上它是一个索引值，指向内核为每一个进程所维护的该进程打开文件的记录表。

# Chp2

## 2.1 套接字协议及其数据传输特性

### 创建套接字

```
#include <sys/socket.h>
int socket(int domain, int type, int protocol)
//domain 套接字中使用的协议族（protocol family）
//type 套接字数据传输类型信息
//protocol 计算机间通信中使用的协议信息
```

#### Protocol Family

| 名称      | 协议族               |
| :-------- | -------------------- |
| PF_INET   | IPv4                 |
| PF_INET6  | IPv6                 |
| PF_LOCAL  | 本地通信的UNIX协议族 |
| PF_PACKET | 底层套接字的协议族   |
| PF_IPX    | IPX Novell协议族     |

套接字中实际采用的最终协议信息是通过socket函数第三个参数传递。在指定的协议族范围内通过第一个参数决定第三个参数。

#### 套接字类型 Type

是指套接字的数据传输方式

##### SOCK_STREAM

这个是面向连接的套接字，一一对应，可靠、按序传递、基于字节的面向连接的数据传输方式的套接字。

##### SOCK_DGRAM

创建面向消息的套接字，强调快速传输、传输数据可能丢失、有数据边界、限制每一次数据大小。

`数据边界`：接受数据的次数应和传输次数相同。

不可靠、无序、以数据的高速传输为目的的套接字。

### 协议的最终选择

因为存在数据传输方式相同，但协议不同。

写`0` 系统会根据情况推断出合适的协议

# Chp3

地址族与数据序列，分配电话号码 `bind()` 

## 用于区分套接字的端口号

端口号就是在同一操作系统内为区分不同套接字设置的。端口号由16位构成。

数据传输目标地址同时包括IP地址和端口号。

## 3.2地址信息的表示

结构体定义，并将地址信息传递给bind函数

```c
struct sockaddr_in 
{
	sa_family_t        sin_family; //地址族（address Family）
	uint16_t           sin_port;   //16位TCP/UDP端口号
	struct in_addr     sin_addr;   //32位IP地址
	char               sin_zero[8];//不使用
}
```

其中 in_addr 定义如下

```c
struct in_addr {
	in_addr_t    s_addr; //32位IPv4地址
}
```

具体含义见POSIX，是为UNIX系统操作系统设立的标准

### sin_family

| 地址族（Address Family） | 含义             |
| :----------------------- | ---------------- |
| AF_INET                  | IPv4             |
| AF_INET6                 | IPv6             |
| AF_LOCAL                 | UNIX协议的地址族 |

### sin_port

16位端口号

### sin_addr

32位IP地址信息，且也以网络字节序保存，结构体

### sin_zore

只是为使结构体sockaddr_in的大小和sockaddr结构体保持一致而插入的成员。必须填充为0。



```c
struct sockaddr_in serv_addr

if (bind(serv_sock, (struct sockaddr *) &serv_addr, sizof(serv_addr)) == -1)
```

重要的bind函数期望得到socketaddr结构体变量地质值，包括地址族、端口号、IP地址，但是直接向socketaddr结构体填充代码会有麻烦

## sockaddr

```c
struct sockaddr
{
	sa_family_t sin_family;  // 地址族
	char 				sa_data[14]; // 地址信息
}
```

sa_data保存的地址信息中需包含IP地址和端口号，剩余部分填充0。但是这包含地址信息来说十分麻烦，因此有了新的结构体sockaddr_in。

## 3.3 网络字节序与地址变换

### 字节序与网络字节序

大端序：高位字节存放在低位地址

小端序：高位字节存放在高位地址

**网络统一字节序为大端序**

### 字节序转换

在填充`sockadr_in`结构体前将数据转换为网络字节序。

```c
unsigned short htons(unsigned short);
unsigned short ntohs(unsigned short);
unsigned long htonl(unsigned long);
unsigned long ntohl(unsigned long);
```

htons 中`h`代表主机host；`n`代表网络network

## 3.4 网络地址的初始化与分配

将IP地址转换为4字节整数型数据。（Dotted Decimal Notation）

将字符串形式的IP地址转换为32位整数型数据

```c
#include <arpa/inet.h>

in_addr_t inet_addr(const char * string);
//成功返回32位大端序整数型值，失败时返回INADDR_NONE。

int inet_aton(const char * string, struct in_addr * addr);
// string 需要转换地址信息，addr，保存地址的变量信息
//成功时返回 1（true），失败时返回0（false）
```

在实际情况下，调用`inet_addr`需要将转换后的IP地址带入`sockaddr_in`结构体中声明`in_addr`结构体中。`inet_aton`函数则不需要这个过程。

**与`inet_aton`函数正好相反的函数，将网络字节序整数型IP地址转换为我们熟悉的字符串形式。**

```c
#include <arpa/inet.h>
char * inet_ntoa(struct in_addr adr);
// 成功返回转换的字符串地址值，失败时返回-1。
```

但是这个函数返回的char指针，意味着这个函数自动将申请了内存空间保存字符串，调用完毕后马上复制到其他空间。因为若再次调用inet_ntoa函数，可能会覆盖之前的字符串信息。

### 网络地址初始化

套接字创建过程中常见的网络地址信息初始化方法

```c
struct sockaddr_in addr;
char * serv_ip = "211.217.168.13"; //声明IP地址字符串
char * serv_port = "9190";         //声明端口号字符串
memset(&addr, 0, sizeof(addr));		 //结果体变量addr的所有成员初始化为 0
addr.sin_family = AF_INET;         //指定地址族
addr.sin_addr.s_addr = inet_addr(serv_ip); //基于字符串的IP地址初始化
addr.sin_port = htons(atoi(serv_port));		 //基于字符串的端口号初始化
```

### INADDR_ANY

每次创建服务器端套接字都要输入IP地址繁琐，`INADDR_ANY` 分配服务器端的IP地址自动获取运行服务器端的计算机IP地址。如果一个主机分配了多个IP地址，则只要端口号一致，就可以从不同的IP地址接受数据

```c
addr.sin_addr.s_addr = htonl(INADDR_ANY);
```

### 向套接字分配网络地址

```c
#include <sys/socket.h>
int bind(int sockfd, struct sockaddr * myaddr, socklen_t addrlen)
//成功返回0，失败返回-1
// sockfd 套接字文件描述符 myaddr 存有地址信息结构体的指针
```

如果这个函数调用成功，则将第二个参数指定的地址信息分配给第一个参数中的相应套接字。

**接下来给出服务器端常见套接字初始化过程**

```c
int serv_sock;
struct sockaddr_in serv_addr;
char * serv_port = "9190";

/*创建服务器端套接字，监听套接字*/
serv_sock = socket(PF_INET, SOCK_STREAM, 0);

/*地址信息初始化*/
memset(&serv_addr, 0, sizeof(serv_addr));
serv_addr.sin_family = AF_INET;
serv_addr.sin_addr.s_addr = htonl(INADDR_ANY);
serv_addr.sin_port = htons(atoi(serv_port));

/*分配地址信息*/
bind(serv_sock, (struct sockaddr *)&serv_addr, sizeof(serv_addr));
```



# Chp4

之前学习了创建套接字和向套接字分配地址

## 4.2 实现基于 TCP 的服务器端和客户端

socket()创建套接字 -> bind()分配套接字地址 -> listen()等待连接请求状态 -> accept()允许连接 -> read()/write()数据交换  -> close() 断开连接

### 进入等待连接请求状态

```c
#include <sys/socket.h>
int listen(int sock, int backlog); //成功返回0，失败返回-1
```

sock 为监听套接字文件描述符， backlog为请求队列的长度，表示最多允许几个连接请求进入队列。

### 受理连接请求

```c
#include <sys/socket.h>
int accept(int sock, struct sockaddr * addr, socklen_t * addrlen); //成功返回文件描述符，失败返回-1 
// sock 为服务器套接字的文件描述符
// addr保存发起连接请求的客户端地址信息的变量地址，调用函数完成后向传递来的地址变量参数填充客户端地址信息
```

### 客户端默认函数调用顺序

socket()创建套接字 -> connect()请求连接 -> read()/write()交换数据 -> close() 断开连接

这里与服务器相比区别就是请求连接，这个是客户端创建套接字后向服务器发起连接

```c
#include <sys/socket.h>
int connect(int sock, struct sockaddr * servaddr, socklen_t addrlen); //成功返回0 失败返回-1
```

客户端调用`connect`函数后，发生以下情况才会返回：

1. 服务器端接收连接请求。
2. 发生断网等异常情况而中断连接请求。

连接请求并不意味着服务器端调用accept函数，其实是服务器端把连接请求信息记录到等待队列中。因此coonect函数返回后并不立即进行数据交换。

**客户端的IP地址和端口在调用connect函数时自动分配，无需调用bind函数进行分配**

## 4.3 实现迭代服务器端/客户端

插入循环语句反复调用`accept`函数

close(client) -> accept() -> read()/write() -> close(client)

# chp5

TCP工作原理

## 5.1 回声客户端的完美实现

回声客户端传输的是字符串，而且是通过调用write函数一次性发送。之后还调用一次read函数，期待着接受自己传输的字符串。理想的客户端应该在收到字符串数据时立即读取并输出。

### 5.1.1 定义应用层协议

回声客户端可以提前知道接收的数据长度，但是我们应该意识到，我们无法预知数据长度

## 5.2 TCP原理

### TCP套接字中的I/O缓冲

TCP套接字的数据收发无边界。服务器端即使调用1次write函数传输40字节的数据，客户端也可能通过4次read函数调用每次读取10字节。

实际上write函数调用后并非立即传输数据，read函数调用后也并非马上接收数据。write函数调用瞬间，数据将移至输出缓冲；read函数调用瞬间，丛输入缓冲读取数据。

### TCP内部工作原理1 TCP套接字从创建到消失

步骤：(1)与对方套接字建立连接 (2)与对方套接字进行数据交换 (3)断开与对方套接字的连接。三次握手

### TCP内部工作原理2 与对方主机的数据交换

ack序号

### TCP内部工作原理3 断开与套接字的连接

4次握手断开连接。

# Chp 6 基于UDP的服务器端/客户端

## UDP套接字的特点

流控制是区分UDP与TCP的最重要的标识。TCP的速度无法超过UDP，但在收发某些类型的数据时有可能接近UDP。例如，每次交换的数据量越大，TCP的传输速率就越接近UDP速率。

UDP最重要的作用就是根据端口号将传到主机的数据包交付给最终的UDP套接字

TCP比UDP慢的原因通常有以下两点：(1)收发数据前后进行的连接设置及清除过程 (2)收发数据过程中为保证可靠性而添加的流控制

## 6.2 实现基于UDP的服务器端/客户端

UDP过程不必调用listen函数和accept函数，**UDP中只有创建套接字的过程和数据交换过程。**

### UDP服务器端和客户端均只需要1个套接字

TCP中，套接字之间应该是一对一的关系。若要向10个客户端提供服务，则除了守门的服务器套接字外，还需要10个服务器端套接字。但在UDP中，不管是服务器还是客户端都只需要1个套接字。

```c
#include <sys/socket.h>
ssize_t sendto(int sock, void *buff, size_t nbytes, int flags, struct sockaddr *to, socklen_t addrlen);
// sock UDP套接字文件描述符
// buff 保存待传输数据的缓冲地址值
// nbytes 待传输的数据长度，以字节为单位
// flags 可选项参数，没有选择传输0
// to 存有目标地址信息的sockaddr 结构体变量的地址值
// addrlen 传递给参数to的地址值结构体变量长度。
```

```c
#include <sys/socket.h>
ssize_t recvfrom(int sock, void *buff, size_t nbytes, int flags, struct sockaddr * from, socklen_t *addrlen)
// 成功时返回接收的字节数，失败时返回-1
// from 保存有发送端地址信息的sockaddr结构体变量的地址值。
// addrlen 保存参数from的结构体变量长度的变量地址
```

**fromIP地址会在开始赋予自己1.0.0.0，在完成recvfrom之后，保存当前UDP传递sock套接字地址信息。**

UDP不同于TCP，不存在请求连接和受理过程，因此在某种意义上无法明确区分服务器端和客户端。

### UDP客户端套接字的地址分配

UDP程序中，调用sendto函数传输数据前应完成对套接字的地址分配工作，因此调用bind函数。当然bind函数在TCP程序中出现过，但bind函数不区分TCP和UDP，也就是说，在UDP程序中同样可以调用。另外如果调用sendto函数时发现尚未分配地址信息，则在首次调用sendto函数时给相应套接字自动分配IP和端口。而且此时分配的地址一直保留到程序结束为止，因此也可用来与其他UDP套接字进行数据交换。

## 6.3 UDP的数据传输特性和调用connect函数

UDP套接字传输的数据包又称数据报，实际上数据报也属于数据包的一种。这与UDP的数据传输性有关，UDP中存在数据边界，1个数据包即可成为1个完整数据，因此成为数据报。

### 已连接(connected)UDP套接字与未连接(unconnected)UDP套接字

TCP套接字中需要注册待传输数据的目标IP和端口号，而UDP中则无需注册。因此，通过sendto函数传输数据的过程大致分为3个阶段：(1)向UDP套接字注册目标IP和端口号 (2)传输数据 (3)删除UDP套接字中注册的目标地址信息。

针对于UDP套接字调用connect函数并不意味着要与对方UDP套接字连接，这只是向UDP套接字注册目标IP和端口信息。之后就与TCP套接字一样，每次调用sendto函数时只需要传输数据。

# Chp7 优雅地断开套接字连接

当主机A发送玩最后的数据后，调用close函数断开连接，之后主机A无法再接收主机B传输的数据。实际上，是无法调用与接收数据相关的函数。最终，由主机B传输的、主机A必须接收的数据也销毁了。为了解决这类问题，“只关闭一部分数据交换中使用的流”(Half-close)

close之间将相互之间通信的两个流全部关闭。

### 针对于优雅断开的shutdown函数

```c
#include <sys/socket.h>
int shutdown(int sock, int howto);
//
//howto 传递断开方式信息。SHUT_RD断开输入流 SHUT_WR断开输出流 SHUT_RDWR同时断开I/O流
```

`SHUT_RD` 断开输入流，套接字无法接受数据。即使输入缓冲收到数据也会抹去，而且无法调用输入相关函数。

`SHUT_WR` 断开输出流，无法传输数据，如果此时输出缓冲还留有未传输的数据，则将传递至目标主机。

`SHUT_RDWR` 相当于分两次调用shutdown

## 完成 `File_Client` 和 `File_Server`

# Chp8 域名及网络地址

DNS(Domain Name System，域名系统)

## 8.1 DNS

使用ping命令用来验证IP数据报是否到达目的地，但是执行过程中会同时经过“域名到IP地址”的转换过程。如果想知道自己计算机中注册的默认DNS服务器地址，使用`nslookup`，在Linus上再加上`server`。

DNS可以视为层次化管理的一种分布式数据库系统。

## 8.2 IP地址和域名之间的转换

### 利用域名获取IP

```c
#include <netdb.h>
struct hostnet * gethostbyname(const char * hostname);
// 成功时返回hostent结构体地址，失败时返回null指针
/*
struct hostent {
char * h_name; //officical name 官方域名
char *8 h_aliases; //alias list 一个IP可以绑定多个域名，除了官方域名外还可以指定其他域名
int h_addrtype; //host address type 地址族信息
int h_length; //address length IP地址长度
char ** h_addr_list; //address list 以整数形式保存域名对应的IP地址
}
*/
```

### Gethostbyname.c

### 利用IP地址获取域名

```c
#include <neetdb.h>
struct hostent * gethostbyaddr(const char * addr, socklen_t len, int family);
// 成功返回hostent结构体变量地址值，失败时返回null
// addr 含有IP地址信息的in_addr结构体指针。
// len 向第一个参数传递的地址信息的字节数
// family 传递地址族信息。
```

### Gethostbyaddr

# Chp9 套接字的多种可选项

## 9.1 套接字可选项和I/O缓冲大小

### 套接字的多种选项

可选项的读取和设置通过如下两个函数完成

```c
#include <sys/socket.h>
int getsockopt(int sock, int level, int optname, void *optval, socklen_t *optlen);
// level 要查看的可选项的协议层
// optname 要查看的可选项名
// optval 保存查看结果的缓冲地址值。第一个值是需要分配缓冲区的大小
// optlen 向第四个参数optval传递的缓冲大小。调用函数，该变量中保存通过第四个参数返回的可选项信息的字节数。
```

成功返回0， 失败返回-1

```c
#include <sys/socket.h>
int setsockopt(int sock, int level, int optname, const void *optval, socklen_t optlen);
```

成功返回0，失败返回-1

### Sock_type.c

**套接字类型只能在创建时决定，以后不能再更改**

### Get_buf.c

### Set_buf.c

## 9.2 SO_REUSEADDR

让客户端先通知服务器端终止程序。在客户端控制台输入Q消息时调用close函数，向服务器端发送FIN消息并经过四次握手过程。当然，输入`CTRL+C`时也会向服务器传递FIN消息。强制终止程序，由操作系统关闭文件及套接字，此过程相当于调用close函数，也会向服务器端传递FIN消息。

当客户端先请求断开连接，所以不会发生特别的事情。重新运行服务器也不会发生意外，但是当服务器断开连接，及强制关闭服务器端，如果使用同一个端口重新运行服务器端，将输出"bind() error"，并且无法再次运行。

因为在第四次握手发送完毕ACK后，服务器经过一段时间Time-wait状态。服务器套接字处于Time-wait过程，相应端口属于正在使用状态。当然在客户端也会存在Time-wait过程。先断开连接的套接字必然会经过Time-wait过程。假设主机A向主机B最后一次握手传输ACK消息后立刻消除套接字。但最后这个ACK丢失未能到达主机A，继而试图重传。但此时主机A已是完全终止的状态，因此主机B永远无法接收到从主机A最后传来的ACK消息。相反，若主机A的套接字处于Time-wait状态，则会向主机B重传最后的ACK消息。

解决方案就是在套接字的可选项中更改SO_REUSEADDR的状态。适当调整该参数，可将Time-wait状态性下的套接字端口号重新分配给新的套接字。

### Reused_eserver.c

## 9.3 TCP_NODELAY

为防止因数据包过多而发生网络过载，Nagle算法诞生，应用于TCP层。

根据传输数据的特性，网络流量未受到太大影响时，不实用Nagle算法要比使用它时传输速度快。最典型的是传输大文件数据。将文件数据传入输出缓冲不会花太多时间。一般情况下，不适用Nagle算法可以提高传输速度。但如果无条件放弃使用Nagle算法，就会增加过多的网络流量。反而会影响传输。

禁用Nagle只需将套接字可选项TCP_NODELAY改为1

```c
int opt_val = 1;
setsockopt(sock, IPPROTO_TCP, TCP_NODELAY, (void*)&opt_val, sizeof(opt_val));
// 查看是否使用
int opt_val;
socklen_t opt_len;
opt_len=sizeof(opt_val);
getsockopt(sock,IPPROTO_TCP,TCP_NODELAY,(void*)&opt_val,&opt_len);
//如果正在使用Nagle算法，opt_val变量会保存0；如果禁用Nagle，则保存。
```

# Chp10 多进程服务器端

网络程序中数据通信时间比CPU运算时间占比更大，具有代表性的并发服务器端实现模型和方法：多进程服务器，多路复用服务器，多线程服务器。

## 10.1 进程概念及应用

### 进程ID

1要分配给操作系统启动后的首个进程。

ps指令可以查看当前运行的所有进程

### 通过fork函数创建进程

```c
#include <unistd.h>
pid_t fork(void);
//成功返回进程ID，失败返回-1
```

父进程：fork函数返回子进程ID；子进程：fork函数返回0。

父进程是指原进程，即调用fork函数的主题，而子进程是通过父进程调用fork函数复制出的进程。

### fork.c

父子进程拥有完全独立的内存结构。

## 进程和僵尸进程

### 产生僵尸进程原因

fork产生子进程的终止方式：传递参数并调用exit函数；main函数中执行return语句并返回

### Zombie.c

后台处理是将控制台窗口中的指令放到后台运行。

```c
./zombie & //&将触发后台处理
ps au //将会看到stat下ZN的僵尸进程
// VSZ 占用虚拟记忆体的大小
// RSS 占用的记忆体大小
// COMMAND 所执行的指令
// 其中stat是该行程的状态:S: 静止状态 D: 无法中断的休眠状态 (通常 IO 的进程) R: 正在执行中 Z: 不存在但暂时无法消除
```

### 销毁僵尸进程1:利用wait函数

**等到任意子进程终止**

```c
#include <sys/wait.h>
pid_t wait(int * statloc);
// 成功时返回终止的子进程ID，失败时返回-1。
```

调用此函数时如果已有子进程终止，那么子进程终止时传递的返回值（exit函数的参数值、main函数的return返回值）将保存到该函数的参数所指内存空间。但函数参数指向的单元中还包含其他信息，因此需要通过下列宏分离

```c
WIFEXITED 子进程正常终止时返回true
WEXITSTATUS 返回子进程的返回值
```

### 销毁僵尸进程2:使用waitpid函数

wait函数会引起程序阻塞，

```c
#include <sys/wait.h>
pid_t waitpid(pid_t pid, int * statloc, int options);
//成功返回终止子进程ID或0，失败时返回-1
//pid等待终止的目标子进程ID，如传递-1，则与wait函数相同，等待任意子进程终止。
//statloc 这个参数将保存子进程的状态信息
```

### Waited.c

## 10.3 信号处理

需要当进程停止工作时给予信号进行消除

### 信号与signal函数

```c
#include <signal.h>
void (*signal(int signo, void(*func)(int)))(int);
//为了在产生信号时调用，返回之前注册的函数指针
//函数名signal
//参数：int signo(特殊情况信息),void(*func)(int)(调用函数指针)
//返回类型：参数为int型，返回void型函数指针
```

上述函数的返回值类型为函数指针

```
#include <unisted.h>
unsigned int alarm(unsigned int seconds);
//返回0或以秒为单位的距SIGALRM信号发生所剩时间。
```

#### Signal.c

### 利用sigaction函数进行信号处理

signal函数在UNIX系统的不同操作系统中可能存在区别，但是signaction函数完全相同

```c
#include <signal.h>
int sigaction(int signo, const struct sigaction * act, struct sigaction * oldact);
//signo 与siganl函数相同，传递信号信息。
//act 对应于第一个参数的信号处理函数信息
//oldact 通过此参数获取之前注册的信号处理函数指针，若不需要则传递0。

/*
struct sigaction {
	void (*sa_handler)(int);// 保存信号处理函数的指针
	sigset_t sa_mask; // 指定信号相关选项 初始值0
	int sa_flags; //特性 初始值0
}
*/
```

#### Sigaction.c

## 利用信号处理技术消灭僵尸

```c
int sigemptyset(sigset_t *set);
//该函数的作用是将信号集初始化为空。
//SIGCHLD 子进程终止时会向父进程发送SIGCHLD信号，告知父进程回收自己
```

## 10.4 基于多任务的并发服务器

利用fork函数，回声服务器（父进程）通过调用accept函数受理连接请求；此时获取的套接字文件描述符创建并传递给子进程；子进程利用传递来的文件描述符提供服务。

#### Echo_Mpserv.c

### 通过fork函数复制文件描述符

父进程将2个套接字(一个是服务器套接字，另一个是与客户端连接的套接字)文件描述符复制给子进程。

调用fork函数时复制了父进程的所有资源，有些人可能认为也会同时复制套接字。但套接字并非进程所有--从严格意义上，套接字属于操作系统--只是进程拥有代表相应套接字的文件描述符。也不一定非要这样理解，仅因为如下原因，复制套接字并不合理“复制套接字后，同一端口将对应多个套接字。”

**1个套接字存在2个文件描述符时，只有2个文件描述符都终止后，才能消除套接字，因此调用fork函数后，要将无关的套接字描述符关掉。**

**父进程保留服务器套接字，子进程保留客户连接套接字**

## 10.5 分割TCP的I/O程序

### 分割优点

**1.客户端父进程负责接受数据，额外创建的子进程负责发送数据。分割后，不同进程分别负责输入和输出，这样，无论客户端是否从服务器端接收完数据都可以进行传输。**

**2.另一个好处是，可以提高频繁交换数据的程序性能。**

# Chp11 进程间通信

### Pipe1.c

```c
#include <unistd.h>
int pipe(int filedes[2])
// 成功时返回0，失败时返回-1
// filedes[0] 管道出口 filedes[1] 管道入口
```

复制用于管道 I/O 的文件描述符，此时父子进程同时拥有I/O文件描述符，重点在于父子进程都可以访问管道的I/O路径，但是子进程仅用于输入路径，父进程用于输出路径。

### 双向通信

建立两个pipe通道，一个用来parent传递给child，一个child传递给parent。

# Chp12 I/O复用

## 理解select函数并实现服务器端

使用select函数可以将多个文件描述符集中到一起统一监视。监视项被称为事情，发生监视项对应情况时，称“发生了事件”。

设置文件描述符->指定监视范围->设置超时->调用select函数->查看调用结果

### 设置文件描述符

利用select函数可以同时监视多个文件描述符。当然，监视文件描述符可以称为监视套接字。此时首先需要将要监视的文件描述符集中到一起。集中式也要安装监视项(接收，传输，异常)进行区分。

```c
FD_ZERO(fd_set * fdset)
FD_SET(int fd, df_set * fdset) //向fd位注册文件描述符fd信息
FD_CLR(int fd, fd_set * fdset) //fd位清除文件描述符fd信息
FD_ISSET(int fd, fd_set * fdset) // 若参数fdset指向的变量中包含文件描述符fd的信息，则返回真
```

### 设置检查（监视）范围及超时

```c
#include <sys/select.h>
#include <sys/time.h>
int select(int maxfd, fd_set * readset, fd_set * writeset, fd_set * exceptset, const struct timeval * timeout); //成功返回大于0的值，该值是发生事件的文件描述符数，失败返回-1，超时返回0
//maxfd 监视对象文件描述符数量
//readset 将所有关注“是否存在待读取数据”的文件描述符注册到fd_set型变量，传递其地址值。
//writeset 将所有关注“是否可传输无阻塞数据”的文件描述符注册到fd_set型变量，传递其地址值
//exceptset 将所有关注“是否发生异常”的文件描述符注册到fd_set型变量，传递其地址值
//timeout 调用时间
```

**文件描述符的监视范围与select函数的第一个参数有关。实际上，select函数要求通过第一个参数传递监视对象文件描述符的数量。因此需要得到注册在fd_set变量中的文件描述数。但每次新建文件描述符时，其值都会增1，故只需将最大的文件描述符值加1再传递到select即可**

# Chp13 多种I/O函数

## 13.1 send&recv 函数

```c
#include <sys/socket.h>
ssize_t send(int sockfd, const void * buf, size_t nbytes, int flags);
// 成功返回发送的字节数，失败返回-1
// sockfd 表述连接的套接字文件描述符
// buf 缓冲数据的缓冲地址值
// nbytes 传输字节数
// flags 自定信息

ssize_t recv(int sockfd, void * buf, size_t nbytes, int flags);
// 成功返回发送的字节数，收到EOF返回0，失败返回-1
// sockfd 表述连接的套接字文件描述符
// buf 缓冲数据的缓冲地址值
// nbytes 传输字节数
// flags 自定信息
```

### MSG_OOB 发送紧急消息

因为紧急消息只能发送一个字节，**因为TCP字节头里包含如下两种信息：URG =1:载有紧急消息的数据包；URG指针：紧急指针位于偏移量为3的位置。**指定MSG_OOB选项的数据本身就是紧急数据包，并通过紧急指针表示紧急消息所在位置。如果传输数据量多于1个字节，除紧急指针的前面1个字节外，数据接收方将通过调用常用输入函数读取剩余部分。换而言之，紧急消息的意义在于督促消息处理，而非紧急传输形式受限的消息。

### 检查输入缓冲

同时设置MSG_PEEK选项和MSG_DONTWAIT选项，以验证输入缓冲中是否存在接收的数据。设置MSG_PEEK选项并调用recv函数时，即使读取量输入缓冲的数据也不会删除，因此该选项通常与MSG_DONTWAIT合作，用于调用非阻塞方式验证待读取数据存在与否的函数

## readv&writev函数

### readv&writev函数

对数据进行整合传输及发送的函数

writev可以将分散保存在多个缓冲中的数据一并发送，readv一并接收

```c
#include <sys/uio.h>
ssize_t writev(int filedes, const struct iovec * iov, int iovcnt);
// 成功时返回发送的字节数，失败时返回-1
// filedes 表示传输对象的套接字文件描述符。
// iov iovec结构体数组的地址值，结构体iovec中包含待发送数据的位置和大小信息。
// iovcnt iov结构体数组的长度
struct iovec {
	void * iov_base; //缓冲地址
	size_t iov_len; //缓冲大小
}
```

```c
#include <sys/uio.h>
ssize_t readv(int filedes, const struct iovec * iov, int iovcnt);
// 成功时返回发送的字节数，失败时返回-1
// filedes 表示传输对象的套接字文件描述符。
// iov iovec结构体数组的地址值，结构体iovec中包含待发送数据的位置和大小信息。
// iovcnt iov结构体数组的长度
```

### 合理使用

合理使用readv和writev

# Chp14 多播与广播

## 1.多播

多播是基于UDP，是一对多。多播服务器针对特定多播组，只发送1次数据。

多播是D类IP地址，加入多播组，可以理解为通过程序完成如下声明：“在D类IP地址中，我希望接收发往目标239.234.218.234的多播数据。”

### 路由Routing和TTL（Time to live，生存时间），以及加入组的方法

设置TTL相关的协议层为IPPROTO_IP，

```c
int send_sock;
int time_live = 64;
send_sock=socket(PF_INET,SOCK_DGRAM,0);
setsockopt(send_sock,IPPROTO_IP, IP_MULTICAST_TTL,(void*)&time_live, sizeof(time_live));
```

加入多播组也通过套接字选项完成，

```c
struct ip_mreq{
  struct in_addr imr_multiaddr;
  struct in_addr imr_interface;
}
//结构定义

int recv_sock;
struct ip_mreq join_adr;
recv_sock=socket(PF_INET,SOCK_DGRAM,0);

join_adr.imr_multiaddr.s_addr="多播组地址";
join_adr.imr_interface.s_addr="加入多播组的主机地址信息"
```

## 广播

多播即使在跨越不同的网络情况下，只要加入多播组就能接收到。广播只能向同一个网络中的主机传输数据。

广播分为两种，直接广播和本地广播，二者主要差别在于IP地址。直接广播的IP地址除了网络地址外，其余主机地址全部设置为1，本地广播使用IP地址限定为255.255.255.255，例如，192.32.23网络中的主机向255.255.255.255传输数据时，数据将传递到192.32.23网络中的所有主机。

# Chp15 套接字和标准I/O

## 15.1 标准I/O函数的优点

### 两个

标准I/O函数具有良好的移植性。标准I/O函数可以利用缓冲提高性能。

I/O缓冲与套接字创建的缓冲属于两个不同的缓冲范围，使用标准I/O传输数据，经过两个缓冲。例如：通过fputs函数传输字符串hello时，首先将数据传递到标准I/O函数的缓冲。然后数据将移动到套接字输出缓冲，最后将字符串发送到对方主机。**设置缓冲的主要目的是为了提高性能，但是套接字的缓冲主要为了实现TCP协议而设立的。**

**实际上，缓冲并非在所有情况下都能带来卓越的性能。但需要传输的数据越多，有无缓冲带来的性能差异越大。可以通过如下两种角度说明性能的提高：传输的数据量；数据向输出缓冲移动的次数。**

### 标准I/O缺点

不容易进行双向通信，有时候可能频繁调用fflush函数，需要以FILE结构体指针的形式返回文件描述符

## 15.2 使用标准I/O函数

通过fdopen函数将创建套接字时返回的文件描述符转换为标准I/O函数中使用的FILE结构体指针。

```c
#include <stdio.h>
FILE * fdopen(int fildes, const char * mode);
// 成功返回FILE结构体指针，失败时返回NULL
// fildes 需要转换的文件描述符
// mode 将要创建的FILE结构体指针的模式信息。
```

利用fileno函数转换为文件描述符，功能与fdopen相反

```c
#include <stdio.h>
int fileno(FILE * stream);
// 成功返回转换后的文件描述符，失败时返回-1
```

## 15.3 基于套接字的标准I/O函数使用

# Chi 16 关于I/O流分离的其他内容

流可以理解为数据收发路径

## 16.1 分离I/O流

之前有两种方法分离I/O流，第一种是TCP I/O过程分离，使用fork函数复制1个文件描述符，以区分输入和输出中使用的文件描述符；第二种是使用fdopen，创建读模式FILE指针和写模式FILE指针。

## 16.2 文件描述符的复制和半关闭

**读模式FILE指针和写模式FILE指针都是基于同一个文件描述符创建的。针对任何一个FILE指针调用fclose函数都会关闭文件描述符，终结套接字**

创建FILE指针前先复制文件描述符即可，复制后另外创建一个文件描述符，然后利用各自的文件描述符生成读模式FILE指针和写模式指针。

调用fork函数是将复制整个进程，因此同一进程内不能同时有原件和副本。但此处讨论的复制并非针对整个过程，而是在同一个进程内完成描述符的复制。

```c
#include <unistd.h>

int dup(int fildes);
int dup2(int fildes1, int fildes2);
// 成功时返回复制的文件描述符，失败时返回-1
// fildes1 需要复制的文件描述符
// fildes2 明确指定的文件描述符整数值
```

dup2函数明确指定复制的文件描述符整数值。向其传递大于0且小于进程能生成的最大文件描述符值时，该值将成为复制出的文件描述符。

# Chp17 优于select的epoll

## 17.1 epoll理解及应用

select复用方法其实由来已久，使用select无论如何优化程序性能也无法同时接入上百个客户端。select不合理地方：调用select函数后常见的针对所有文件描述符的循环语句；每次调用select函数时都需要向该函数传递监视对象信息。

### 实现epoll时必要的函数和结构体

能够克服select函数缺点的epoll函数具有如下优点：无需编写以监视状态变化为目的的针对所有文件描述符的循环语句；调用对应于select函数的epoll_wait函数时无需每次传递监视对象信息。

epoll方式中通过结构体`epoll_event`将发生变化的(发生事件的)文件描述符单独集中到一起。

```c
struct epoll_event {
	__uint32_t events;
	epoll_data_t data;
}

typedef union epoll_data {
	void * ptr;
	int fd;
	__uint32_t u32;
	__uint64_t u64;
} epoll_data_t;
```

**声明足够大的epoll_event结构体数组后，传递给epoll_wait函数时，发生变化的文件描述符信息将被填入该数组。**

```c
#include <sys.epoll.h>
int epoll_create(int size);
// 成功时返回epoll文件描述符，失败时返回-1
// size 大小
```

`epoll_create`函数创建的文件描述符保存空间成为“epoll例程”，参数size传递的值决定epoll例程的大小，但是该值只是向操作系统提的建议。创建的资源与套接字相同。

**生成epoll例程后，应在其内部注册监视文件描述符，使用epoll_ctl函数。**

```c
#include <sys/epoll.h>
int epoll_ctl(int epfd, int op, int fd, struct epoll_event * event);
// 成功返回0，失败返回-1.
// epfd 用于注册监视对象的epoll例程的文件描述符。
// op 用于指定监视对象的添加、删除或更改等操作
// fd 需要注册的监视对象文件描述符
// event 监视对象的事件类型
// EPOLL_CTL_ADD 文件描述符注册到epoll例程
// EPOLL_CTL_DEL 从epoll例程中删除文件描述符
// EPOLL_CTL_MOD 更改注册的文件描述符关注事件发生情况
epoll_ctl(A,EPOLL_CTL_ADD,B,C); //例程A中注册文件描述符B，主要目的是监视参数C中的事件
epoll_ctl(A,EPOLL_CTL_DEL,B,NULL); //从epoll例程A中删除文件描述符B
```

等待epoll事件从epoll实例中发生， 并返回事件以及对应文件描述符。

```c
#include <sys/epoll.h>
int epoll_wait(int epfd, struct epoll_event * events, int maxevents, int timeout);
// 返回捕获事件个数，或者理解为“成功时返回发生事件的文件描述符个数”，失败返回-1
// epfd 表示事件发生监视范围的epoll例程的文件描述符
// event 保存发生事件的文件描述符集合的结构体地址值
// maxevent 第二个参数中可以保存的最大事件数，期望捕获的事件个数
// timeout 传递-1时，一直等待直到发生事件
```

`epoll_create`只是在内核初始化一下数据结构然后返回fd

## 17.2 条件触发和边缘触发

往往无法正确区分条件触发（Level Trigger）和边缘触发（Edge Trigger）

**条件触发方式中，只要输入缓冲有数据就会一直通知该事情。**

**边缘触发中输入缓冲收到数据时仅注册1次该事件。即使输入缓冲中还有数据，也不会再进行注册。**

### 边缘触发

1.通过errno变量验证错误原因 2.为了完成非阻塞（Non- blocking）I/O，更改套接字特性。

Linux提供更改或读取文件属性的如下方法

```c
#include <fcntl.h>
int fcntl(int filedes, int cmd, ...);
//成功返回cmd参数相关值，失败时返回-1
// fildes属性更改目标的文件描述符
// cmd 表示函数调用的目的
```

fcntl具有可变参数的形式。如果向第二个参数传递F_GETFL，可以获得第一个参数所指的文件描述符属性(int)。反之，如果传递F_SETFL，可以更改文件描述符属性。如果希望将文件（套接字）改为非阻塞模式，需要如下2条语句

```c
int flag = fcntl(fd, F_GETFL, 0); //获取之前设置的属性信息
fcntll(fd, F_SETFL, flag|O_NONBLOCK); // 通过第二天语句在此基础上添加非阻塞O_NONBLOCK标识
```

边缘触发方式中，接收数据时仅注册1次该事件。就因为这种特点，一旦发生输入相关事件，就应该读取输入缓冲中的全部数据。因此需要验证输入缓冲是否为空。

**在边缘触发方式下，以阻塞方式工作的read&write函数有可能引起服务器端的长时间停顿。**

### 条件触发和边缘触发孰优孰劣

#### 条件触发

只要输入缓冲有数据就会一直触发事件，直到缓冲区没有数据。

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/epoll.h>
int main() {
    int epfd, nfds;
    char buf[256];
    struct epoll_event event, events[5];
    epfd=epoll_create(1);
    event.data.fd = STDIN_FILENO;
    event.events = EPOLLIN; //LT默认模式
    epoll_ctl(epfd,EPOLL_CTL_ADD,STDIN_FILENO, &event);
    while (1) {
        nfds = epoll_wait(epfd, events, 5, -1);
        int i;
        for (i = 0; i < nfds; ++i) {
            if (events[i].data.fd == STDIN_FILENO);
            //read(STDIN_FILENO, buf, sizeof(buf));
            printf("hello world\n");
        }
    }
    return 0;
}
```

使用键盘输入字符串的时候，字符串会输入到STDIN_FILENO文件描述符中亦或是在其缓冲中，但是此时没有read函数将其读出，所以就一直存储在缓冲中，因此会一直有条件触发。

#### 边缘触发

```c
#include <unistd.h>
#include <stdio.h>
#include <sys/epoll.h>
 
int main(int argc, char *argv[])
{
    int epfd, nfds;
    char buf[256];
    struct epoll_event event, events[5];
    epfd = epoll_create(1);
    event.data.fd = STDIN_FILENO;
    event.events = EPOLLIN | EPOLLET;  // 加入EPOLLET即可变为边缘模式
    epoll_ctl(epfd, EPOLL_CTL_ADD, STDIN_FILENO, &event);

    while (1) {
        nfds = epoll_wait(epfd, events, 5, -1); // 
        int i;
        for (i = 0; i < nfds; ++i) {
            if (events[i].data.fd == STDIN_FILENO) {
                //read(STDIN_FILENO, buf, sizeof(buf));
                printf("hello world\n");
            }
        }
    }
    return 0;
}
```

#### 阻塞和非阻塞I/O

阻塞I/O：当你去读一个阻塞的文件描述符时，如果在该文件描述符上没有数据可读，那么它会一直阻塞(通俗一点就是一直卡在调用函数那里)，直到有数据可读。当你去写一个阻塞的文件描述符时，如果在该文件描述符上没有空间(通常是缓冲区)可写，那么它会一直阻塞，直到有空间可写。以上的读和写我们统一指在某个文件描述符进行的操作，不单单指真正的读数据，写数据，还包括接收连接accept()，发起连接connect()等操作…
非阻塞I/O：当你去读写一个非阻塞的文件描述符时，不管可不可以读写，它都会立即返回，返回成功说明读写操作完成了，返回失败会设置相应errno状态码，根据这个errno可以进一步执行其他处理。它不会像阻塞IO那样，卡在那里不动！！！
**边缘触发条件下，以阻塞方式工作的 read & write 函数有可能引起服务端的长时间停顿。因此，边缘触发方式中一定要采用非阻塞 read & write 函数**

# Chp18 多线程服务器端的实现

多个线程将共享数据区和堆。进程：在操作系统构成单独执行流的单位；线程：在进程构成单独执行流的单位。

### 线程的创建和执行流程

线程具有单独的执行流，因此需要单独定义线程的main函数

```c
#include <pthread.h>
int pthread_create(pthread_t * restrict thread, const pthread_attr_t * restrict attr, void * (* start_routine)(void *), void * restrict arg);
// 成功返回0，失败时返回其他值
// thread 保存新创建线程ID的变量地址值。
// attr 用于传递线程属性的参数，传递null时，创建默认属性的线程
// start_routine 相当于线程的main函数、在单独执行流中执行的函数地址值
// arg 通过第三个参数传递调用函数时包含传递参数信息的变量地址值
```

**线程相关编译需要添加-lpthread选项声明需要连接线程库，只要这样才能调用pthread.h中声明的参数**

调用`pthread_join`函数的进程或线程将进入等待状态，直到第一个参数为ID的线程终止为止。

```c
#include <pthread.h>
int pthread_join(pthread_t thread, void ** status);
// 成功时返回0，失败时返回其他值
// thread 该参数值ID的线程终止后才会从该函数返回
// status 保存线程的main函数返回值的指针变量地址值
```

### 可在临界区内调用的函数

线程安全函数与非线程安全函数

线程安全函数的名称后缀通常为`_r`，可以使用**声明头文件前定义_REENTRANT宏**，自动将非_t函数当作带后缀的函数进行调用。

```
gcc -D_REENTRANT mythread.c -o mthread -lpthread
```

### 工作线程模型

### 线程同步

线程同步用于解决线程访问顺序引发的问题。**同时访问同一内存空间时发生的情况，需要指定访问的同一内存空间的线程执行顺序的情况。**

互斥量的创建以及销毁函数

```c
#include <pthread.h>
int pthread_mutex_init(pthread_mutex_t * mutexm, const pthread_mutexattr_t * attr);
int pthread_mutex_destory(pthread_mutex_t * mutex);
// 成功返回0，失败返回其他值
// mutex创建互斥量时传递保存互斥量的变量地址值，销毁时传递需要销毁的互斥量地址值。
// 传递即将创建的互斥量属性，没有特别需要指定的属性时传递NULL。
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER //宏定义说明
```

互斥量锁住或释放临界区时使用的函数

```c
#include <pthread.h>
int pthread_mutex_lock(pthread_mutex_t * mutex);
int pthread_mutex_unlock(pthread_mutex_t * mutex);
```

可以使用如下方式保护临界区

```c
pthread_mutex_lock(&mutex);
// start
// end
pthread_mutex_unlock(&mutex);
```

### 信号量

信号量创建及销毁方法

```c
#include <semaphore.h>
int sem_init(sem_t * sem, int pshared, unsigned int value);
int sem_destory(sem_t * sem);
// 成功返回0，失败返回其他值
// sem创建信号量时传递保存信号量的变量地址值，销毁时传递需要销毁的信号量变量地址值
// pshared传递其他值，创建可由多个进程共享的信号量 传递0时，创建只允许1个进程内部使用的信号量。我们需要完成同一进程内的线程同步，传递0。
// value 指定新创建的信号量初始值
```

```c
#include <semaphore.h>
int sem_post(sem_t * sem);
int sem_wait(sem_t * sem);
//成功返回0，失败返回其他值
// sem传递保存信号量读取值的变量地址值，传递给sem_post时信号量增1，传递给sem_wait时信号量减1.
```

信号量的值不能小于0，但为0时调用sem_wait函数，调用函数的线程将会进入阻塞状态。当有其他的调用sem_post函数时，信号量将变为1，而原本阻塞的线程可以通过该信号量重新减为0跳出阻塞状态。

```c
sem_wait(&sem); //信号量变为0
//start
//end
sem_post(&sem); //信号量变为1 
```

## 销毁线程

### 三种方法

Linux线程并不是在首次调用的线程main函数返回时自动销毁，所以用如下2种方法之一加以明确。否则由线程创建的内存空间将一直存在。

```c
pthread_join pthread_detach
```

调用pthread_join函数，调用该函数，不仅会等待线程终止，还会引导线程销毁。但是该函数的问题是，线程终止前，调用该函数的线程将会进入阻塞状态。因此通过使用如下函数引导线程销毁。

```c
#include <pthread.h>
int pthread_detach(pthread_t thread);
// 成功返回0，失败返回其他值
// thread终止的同时需要销毁的线程ID
```

上述函数不会引起线程终止或进入阻塞状态，可以通过该函数引导销毁线程创建的内存空间。调用该函数后不能再针对相应线程调用pthread_join函数。
