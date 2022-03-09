# TCP/IP 网络编程

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

```
#include <sys/socket.h>
int listen(int sockfd, int backlog);
//成功返回0，失败返回-1
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
ssize_t write(int fd, const void * buf, size_t nbytes);
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

```
struct sockaddr_in 
{
	sa_family_t        sin_family; //地址族（address Family）
	uint16_t           sin_port;   //16位TCP/UDP端口号
	struct in_addr     sin_addr;   //32位IP地址
	char               sin_zero[8];//不使用
}
```

其中 in_addr 定义如下

```
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

## sockaddr

```
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

```
unsigned short htons(unsigned short);
unsigned short ntohs(unsigned short);
unsigned long htonl(unsigned long);
unsigned long ntohl(unsigned long);
```

htons 中`h`代表主机host；`n`代表网络network

## 3.4 网络地址的初始化与分配

将IP地址转换为4字节整数型数据。（Dotted Decimal Notation）

将字符串形式的IP地址转换为32位整数型数据

```
#include <arpa/inet.h>

in_addr_t inet_addr(const char * string);
//成功返回32位大端序整数型值，失败时返回INADDR_NONE。

int inet_aton(const char * string, struct in_addr * addr);
// string 需要转换地址信息，addr，保存地址的变量信息
//成功时返回 1（true），失败时返回0（false）
```

在实际情况下，调用`inet_addr`需要将转换后的IP地址带入`sockaddr_in`结构体中声明`in_addr`结构体中。`inet_aton`函数则不需要这个过程。

**与`inet_aton`函数正好相反的函数，将网络字节序整数型IP地址转换为我们熟悉的字符串形式。**

```
#include <arpa/inet.h>
char * inet_ntoa(struct in_addr adr);
// 成功返回转换的字符串地址值，失败时返回-1。
```

但是这个函数返回的char指针，意味着这个函数自动将申请了内存空间保存字符串，调用完毕后马上复制到其他空间。因为若再次调用inet_ntoa函数，可能会覆盖之前的字符串信息。

### 网络地址初始化

套接字创建过程中常见的网络地址信息初始化方法

```
struct sockaddr_in addr;
char * serv_ip = "211.217.168.13"; //声明IP地址字符串
char * serv_port = "9190";         //声明端口号字符串
memset(&addr, 0, sizeof(addr));		 //结果体变量addr的所有成员初始化为 0
addr.sin_family = AF_INET;         //指定地址族
addr.sin_addr.s_addr = inet_addr(serv_ip); //基于字符串的IP地址初始化
addr.sin_port = htons(atoi(serv_port));		 //基于字符串的端口号初始化
```

### INADDR_ANY

每次创建服务器端套接字都要输入IP地址繁琐，`INADDR_ANY` 分配服务器端的IP地址自动获取运行服务器端的计算机IP地址。如果一个主机分配了多个IP地址，则只要端口号一致，就可以从不同的IP地址接受数据。

```
addr.sin_addr.s_addr = htonl(INADDR_ANY);
```
