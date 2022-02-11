# Docker使用

[详细介绍文档](https://blog.csdn.net/q610376681/article/details/90483576)

## Docker的理解：

## Docker的安装过程：

[安装网址](https://www.runoob.com/docker/ubuntu-docker-install.html) 

Docker是建立在Linux系统之上。



[Docker命令找不到](https://blog.csdn.net/footrip/article/details/103212619) 这个链接解决问题就是出现了
$$
sudo: docker: command   not found
$$
我们在完成docker镜像制作后，我们要是想使用docker，先要创建容器，这个容器中运行image(镜像)

[参考网址docker的简单应用](https://www.cnblogs.com/lixaingyang/p/11976827.html)

遇到一个问题就是docker容器链接不上外边的数据库，所以我docker又拉一个数据库 [操作流程](https://www.cnblogs.com/haoxianrui/p/14397987.html)

containerd 将 Docker 镜像转换为 ODI bundle，并让runc 基于此创建一个新的容器

Docker 守护进程始终以 root 身份运行；Docker 安装完成后，需要确认 Docker 的守护进程。Docker是使用root 权限运行他的程序，进而可以处理普通用户无法完成的操作(比如挂载文件系统)。配置Docker 的守护进程：运行Docker 时，可以使用 -H 标志，调整守护进程绑定监听接口的方式。

```
$ sudo /usr/bin/docker -d -H tcp://0.0.0.0:2375
```

在使用 Upstart 的 Ubuntu 系统下，Docker守护进程生成的日志输出都保存在 /var/log/upstart/docker.log 文件中。

基本命令

```
$ sudo status docker     # 检查 Docker守护进程是否运行中
$ sudo stop docker       # 停止Docker的守护进程
$ sudo start docker      # 启动Docker守护进程
```

## Linux 的安装后步骤

### 以非root用户 管理Docker

1.创建docker 组

```
$ sudo groupadd docker
```

2.将用户添加到docker 组

```
$ sudo usermod -aG docker $USER
```

