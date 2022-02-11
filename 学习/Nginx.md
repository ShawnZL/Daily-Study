# Nginx

nginx是一个功能强大的web服务器加反向代理服务器，在项目使用过程中，使用最多的三个功能是**反向代理**，**负载均衡**和**静态服务器**。

```
main                                # 全局配置

events {                            # nginx工作模式配置

}

http {                                # http设置
    ....

    server {                        # 服务器主机配置
        ....
        location {                    # 路由配置
            ....
        }

        location path {
            ....
        }

        location otherpath {
            ....
        }
    }

    server {
        ....

        location {
            ....
        }
    }

    upstream name {                    # 负载均衡配置
        ....
    }
}
```

上边的配置文件主要有6个部分组成

1.main：用于nginx全局信息的配置

2.events：用于nginx工作模式的配置

3.http：用于进行http协议信息的一些配置

4.server：用于进行服务器访问信息的配置

5.location：用于进行访问路由的配置

6.upstream：用于进行负载均衡的配置

## main 模块

```
# user nobody nobody;
worker_processes 2;
# error_log logs/error.log
# error_log logs/error.log notice
# error_log logs/error.log info
# pid logs/nginx.pid
worker_rlimit_nofile 1024;
```

上述配置都是存放在main全局配置模块中的配置项

**user**用来指定nginx worker进程运行用户以及用户组，默认nobady账号运行。

**worker_processes**指定nginx要开启的子进程数量，运行过程中监控每个进程消耗内存。

**error_log**定义错误日志文件的位置及输出级别 \[debug / info / notice / warn / error / crit\]

**pid**用来指定进程id存储文件位置

**worker_rlimit_nofile**用于指定一个进程可以打开最多文件数量的描述

## event 模块

```
event {
    worker_connections 1024;
    multi_accept on;
    use epoll;
}
```

上述配置是针对nginx服务器的工作模式的一些操作配置

**worker_connections**指定最大可以同时接受的连接数量，注意，最大连接数量是和worker_processes共同决定的。

**multi_accept**配置指定nginx在收到一个新连接通知后，尽可能多的接受更多连接

**use epoll**配置线程轮询的方法，如果是linux使用epoll，如果是BSD如Mac请使用Kqueue

## http模块

```
http {
    ##
    # 基础配置
    ##

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    # server_tokens off;

    # server_names_hash_bucket_size 64;
    # server_name_in_redirect off;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    ##
    # SSL证书配置
    ##

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
    ssl_prefer_server_ciphers on;

    ##
    # 日志配置
    ##

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    ##
    # Gzip 压缩配置
    ##

    gzip on;
    gzip_disable "msie6";

    # gzip_vary on;
    # gzip_proxied any;
    # gzip_comp_level 6;
    # gzip_buffers 16 8k;
    # gzip_http_version 1.1;
    # gzip_types text/plain text/css application/json application/javascript
 text/xml application/xml application/xml+rss text/javascript;

    ##
    # 虚拟主机配置
    ##

    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
```

### （1）基础配置

```
sendfile on：配置on让sendfile发挥作用，将文件的回写过程交给数据缓冲去去完成，而不是放在应用中完成，这样的话在性能提升有有好处
tc_nopush on：让nginx在一个数据包中发送所有的头文件，而不是一个一个单独发
tcp_nodelay on：让nginx不要缓存数据，而是一段一段发送，如果数据的传输有实时性的要求的话可以配置它，发送完一小段数据就立刻能得到返回值，但是不要滥用哦

keepalive_timeout 10：给客户端分配连接超时时间，服务器会在这个时间过后关闭连接。一般设置时间较短，可以让nginx工作持续性更好
client_header_timeout 10：设置请求头的超时时间
client_body_timeout 10:设置请求体的超时时间
send_timeout 10：指定客户端响应超时时间，如果客户端两次操作间隔超过这个时间，服务器就会关闭这个链接

limit_conn_zone $binary_remote_addr zone=addr:5m ：设置用于保存各种key的共享内存的参数，
limit_conn addr 100: 给定的key设置最大连接数

server_tokens：虽然不会让nginx执行速度更快，但是可以在错误页面关闭nginx版本提示，对于网站安全性的提升有好处哦
include /etc/nginx/mime.types：指定在当前文件中包含另一个文件的指令
default_type application/octet-stream：指定默认处理的文件类型可以是二进制
type_hash_max_size 2048：混淆数据，影响三列冲突率，值越大消耗内存越多，散列key冲突率会降低，检索速度更快；值越小key，占用内存较少，冲突率越高，检索速度变慢
```

### （2）日志配置

```
access_log /var/log/nginx/access.log; 设置存储访问记录的日志
error_log /var/log/nginx/error.log; 设置存储记录错误发生的日志
```

### （3）SSL证书加密

```
ssl_protocols：指令用于启动特定的加密协议，nginx在1.1.13和1.0.12版本后默认是ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2，TLSv1.1与TLSv1.2要确保OpenSSL >= 1.0.1 ，SSLv3 现在还有很多地方在用但有不少被攻击的漏洞。
ssl prefer server ciphers：设置协商加密算法时，优先使用我们服务端的加密套件，而不是客户端浏览器的加密套件
```

### （4）压缩配置

```
gzip 是告诉nginx采用gzip压缩的形式发送数据。这将会减少我们发送的数据量。
gzip_disable 为指定的客户端禁用gzip功能。我们设置成IE6或者更低版本以使我们的方案能够广泛兼容。
gzip_static 告诉nginx在压缩资源之前，先查找是否有预先gzip处理过的资源。这要求你预先压缩你的文件（在这个例子中被注释掉了），从而允许你使用最高压缩比，这样nginx就不用再压缩这些文件了（想要更详尽的gzip_static的信息，请点击这里）。
gzip_proxied 允许或者禁止压缩基于请求和响应的响应流。我们设置为any，意味着将会压缩所有的请求。
gzip_min_length 设置对数据启用压缩的最少字节数。如果一个请求小于1000字节，我们最好不要压缩它，因为压缩这些小的数据会降低处理此请求的所有进程的速度。
gzip_comp_level 设置数据的压缩等级。这个等级可以是1-9之间的任意数值，9是最慢但是压缩比最大的。我们设置为4，这是一个比较折中的设置。
gzip_type 设置需要压缩的数据格式。上面例子中已经有一些了，你也可以再添加更多的格式。
```

### （5）文件缓存配置

```
open_file_cache 打开缓存的同时也指定了缓存最大数目，以及缓存的时间。我们可以设置一个相对高的最大时间，这样我们可以在它们不活动超过20秒后清除掉。
open_file_cache_valid 在open_file_cache中指定检测正确信息的间隔时间。
open_file_cache_min_uses 定义了open_file_cache中指令参数不活动时间期间里最小的文件数。
open_file_cache_errors 指定了当搜索一个文件时是否缓存错误信息，也包括再次给配置中添加文件。我们也包括了服务器模块，这些是在不同文件中定义的。如果你的服务器模块不在这些位置，你就得修改这一行来指定正确的位置。
```

## server模块

server模块配置是http模块中的一个子模块，用来定义一个虚拟访问主机，也就是一个虚拟服务器的配置信息。

```
server {
    listen        80;
    server_name localhost    192.168.1.100;
    root        /nginx/www;
    index        index.php index.html index.html;
    charset        utf-8;
    access_log    logs/access.log;
    error_log    logs/error.log;
    ......
}
```

**server**：一个虚拟主机的配置，一个http中可以配置多个server。

**server_name**：指定ip地址或者域名，多个配置之间用空格分割

**root**：表示整个server虚拟主机内的根目录，所有当前主机web项目的根目录

**index**：用户访问web网站时的全局首页

**charset**：用于设置www/路径中配置的网页的默认编码格式

**access_log**：用于指定该虚拟主机服务器中的访问记录日志存放路径

**error_log**：用于指定该虚拟主机服务器中访问错误日志的存放路径

## location模块

location模块时nginx配置中出现最多的一个配置，主要用于配置路由访问信息

在路由访问信息配置中关联到反向代理，负载均衡等等各项功能

```
location / {
    root    /nginx/www;
    index    index.php index.html index.htm;
}
```

**location /**：表示匹配访问根目录

**root**：用于指定访问根目录时，访问虚拟主机的web目录

**index**：在不指定访问具体资源时，默认展示的资源文件列表

## 反向代理配置方式

通过反向代理代理服务器访问模式，通过proxy_set配置让客户端访问透明化

```
location / {
    proxy_pass http://localhost:8888;
    proxy_set_header X-real-ip $remote_addr;
    proxy_set_header Host $http_host;
}
```

## uwsgi配置

WSGI (Web Server Gateway Interface) 网关，WSGI就像一座桥梁，一边连接着Web服务器，另一边连着用户的应用。WSGI有两方：“服务器”或“网关”一方，以及“应用程序”或“应用框架”一方。服务方调用应用方，提供环境信息，以及一个回调函数（提供给应用程序用来将消息头传递给服务器方），并接收Web内容作为返回值。

所谓的 WSGI中间件同时实现了API的两方，因此可以在WSGI服务和WSGI应用之间起调解作用：从WSGI服务器的角度来说，中间件扮演应用程序，而从应用程序的角度来说，中间件扮演服务器。“中间件”组件可以执行以下功能：

- 重写环境变量后，根据目标URL，将请求消息路由到不同的应用对象。
- 允许在一个进程中同时运行多个应用程序或应用框架。
- 负载均衡和远程处理，通过在网络上转发请求和响应消息。
- 进行内容后处理，例如应用XSLT样式表。

uWSGI是一个Web服务器，它实现了WSGI协议。

wsgi模式下的服务器配置访问方式

```
location / {
    include uwsgi_params;
    uwsgi_pass localhost:8888
}
```

## upstream模块

upstream模块主要负责负载均衡的配置，通过默认的轮询调度方式来分发请求到后端服务器

```
upstream name {
    ip_hash;
    server 192.168.1.100:8000;
    server 192.168.1.100:8001 down;
    server 192.168.1.100:8002 max_fails=3;
    server 192.168.1.100:8003 fail_timeout=20s;
    server 192.168.1.100:8004 max_fails=3 fail_timeout=20s;
}
```

**ip_hash**：指定请求调度算法，默认是weight权重轮询调度，可以指定

**server host**：分发服务器的列表信息

**-- down**：表示该主机暂停服务

**-- max_fails**：表示失败最大次数，超过失败最大次数暂停服务

**-- fail_timeout**：表示如果请求受理失败，暂停指定的时间之后重新发起请求