# TCP/IP 网络编程

## chp1

### 1.1

套接字：用来连接该网络的工具

#### socket()  bind() listen() accept()

`socket()` 创建套接字，安装电话机

```
#include <sys/socket.h>
int socket(int domain, int type, int protocol);
//成功时返回文件描述符，失败返回-1
```

`bing()` 给套接字分配地址，分配电话号码

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

### 1.2 基于Linux的文件操作

#### 底层文件访问 low-level file Access 和文件描述符 file Descriptor

分配给标准输入输出及标准错误的文件描述符

| 文件描述符 | 对象                      |
| :--------- | ------------------------- |
| 0          | 标准输入：Standard Input  |
| 1          | 标准输出：Standard Output |
| 2          | 标准错误：Standard Error  |

文件和套接字一般经过创建过程才会被分配文件描述符。以上表未经过特殊创建也会自动分配

#### 打开文件

```
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
int open(const char *path, int flag)
// path:文件名字符串的地址 flag：文件打开模式信息
```

#### 关闭文件

```
#include <unistd.h>
int close(int fd); //成功返回0，失败返回-1
//fd 需要关闭的文件或套接字的文件描述符
```

#### 将数据写入文件

```
#include <unistd.h>
ssize_t write(int fd, const void * buf, size_t nbytes);
//fd 显示数据传输对象的文件描述符，buf 保存要传输数据的缓冲地址值，nbytes 要传输数据的字节数
//成功返回字节数，失败返回-1
```

`size_t` 使通过`typedef` 声明的 `unsigned int`，多加的`s` 相当于 `signed`。`ssize_t`是通过 typedef 声明的 `signed int` 类型。

##### 后缀为`_t` 的数据类型

都是元数据类型（primitive），在`sys/types.h`中由typedef声明定义，就是在不同机器中数据位数定义不统一，减少代码改动。例如`int有16位和32位，根据系统不同而变化。

#### 文件描述符与套接字

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

