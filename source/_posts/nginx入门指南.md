---
title: Nginx入门指南
date: 2019-09-06 13:52:09
categories: 
  - 服务器
tags: 
  - Nginx
---

## 简述 Nginx
- nginx有一个主进程和几个工作进程。(参看 worker_processes).
  - 主进程的主要目的是为了读取和评估配置并且维护工作进程。工作进程处理实际请求。
  - nginx在工作进程中采用事件驱动模型和OS依赖机制有效分配请求。
  - 工作进程的数量取决于配置文件中定义的固定值或者是通过有效的CPU核数自动判定数量。
  - 工作进程的数量在配置文件中定义，可据给定的配置对工作进程的数量进行固定，也可以根据可用CPU内核的数量进行自动调整。

- nginx及其各模块的工作方式取决于配置文件。
  - 默认情况下，配置文件名为nginx.conf，并放置在/usr/local/nginx/conf、/etc/nginx或/usr/local/etc/nginx.目录中。

## 安装
> 自行参考 Installing nginx 页面
```bash
> brew install ngnix
```

## 启动, 停止, 和重新加载配置文件
> 启动的时候直接运行nginx.exe或者nginx。如果nginx已经启动，就可以直接使用参数-s 来进行调用执行，具体的使用语法如下：

```bash
> nginx -s signal
```
signal 的位置可以是如下参数：
- stop — 指令一经发出，直接停止
- quit — 等待所有的请求完成，再停止
- reload — 重新加载配置文件nginx.conf
- reopen — 重新打开日志文件
  
## 配置文件结构
- nginx由模块组成
  - 这些模块由配置文件中指定的指令控制。
  - 指令分为简单指令和块指令。
  - 一个简单的指令由名称和参数组成，这些名称和参数由空格分隔，并以分号(;)结尾。
  - 块指令具有与简单指令相同的结构，但它的结尾不是分号，而是一组由大括号({、})包围的附加指令。
  - 如果一个块指令在大括号中包含其他指令，则这个块指令又叫做上下文 (例如: events, http, server, 和location).
  - 放在任何上下文之外的配置文件中的指令被认为是在主上下文中。
  - event 和 http 指令驻留在主上下文中，server 驻留在 http 中，location 驻留在 server中。
  - 一行中在#符号后面的是注释
  
### 全局块
  - 从配置文件开始到 events 块之间的内容
  - 主要设置影响 Ngnix 服务器整体运行的配置指令

### event 块
  - 涉及的指令主要影响 Nginx 服务器与用户的网络连接
  - 这部分的配置对 Nginx 的性能影响较大，在实际中应该灵活配置
  
### http 块
  - 是 Nginx 服务器配置中最频繁的部分，代理、缓存和日志定义等绝大多数功能和第三方模块的配置都在这里
  - http 块中也可以包括 http 全局块、 server块
  
### http 全局块
  - http全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。
  
### sever 块
  - 这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。后面会详细介绍虚拟主机的概念。
  - 每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。
  - 而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。
  
### location 块
  - 一个 server 块可以配置多个 location 块。
  - 这块的主要作用是基于 Nginx 服务器接收到的请求字符串（例如 server_name/uri-string），对虚拟主机名称（也可以是IP别名）之外的字符串（例如 前面的 /uri-string）进行匹配，对特定的请求进行处理。**地址定向、数据缓存和应答控制**等功能，还有许多第三方模块的配置也在这里进行。
  
## 功能
  - web服务器最重要的任务是对外提供文件，例如(图片或者静态HTML网页)。

### 提供静态资源内容
- 通常，配置文件包含几个server块指令，这些块指令通过不同的服务名字（server names）分发监听（distinguished listen）在不同的端口。
- 一旦nginx决定哪个服务器处理请求，它就会根据 server 块指令中定义的 location 指令的参数匹配请求头中指定的URI。
- 在server块指令中添加 location 指令：
```
location / {
 root /data/www;
}
````
> 这个location块详述了与URI请求相匹配的“/”前缀。如果匹配到请求，这个 URI 会被转到 root 指令的路径。也就是说，所有请求文件都会直接请求到本地文件系统的/data/www路径。***如果有多个匹配的location块，nginx会选择最长前缀的那个***。上面提供的location块是长度只有1的最短前缀，因此，其它所有的location块都匹配失败的话，这个location块指令才会被使用。
- 添加第二个location块：
```
location /images {
 root /data;
}
```
> 这个会匹配以/images开头的请求（/也会匹配请求，只不过这个前缀更短）。

- 一个正常的服务器配置文件监听在80端口上，并且可以在本机上成功访问http://localhost/。
- 以/images/开头请求的URI地址，服务器会从/data/images文件夹下面返回对应的文件。例如，请求/data/images/example.png这个文件，nginx服务器会返回http://localhost/images/example.png。
- 如果服务器没有这个文件会返回404 错误。不是以/images/开头的请求,就会被映射到/data/www 文件夹。例如, 请求nginx/data/www/some/example.html,服务器会响应http://localhost/some/example.html 这个文件。

- 为了应用一个新的配置文件，如果nginx还没有启动，就直接启动nginx服务器，如果已经启动，直接使用下面指令发送到nginx的主进程中，如下: 如果nginx尚未启动，则启动nginx，或者向nginx的主进程发送重新加载信号，执行:
```bash
> nginx -s reload
```

### 反向代理
  - 正向代理代理客户端，反向代理代理服务器
  - 反向代理，其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，在返回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实服务器IP地址。

#### 反向代理配置
  - 例：使用 Ngwmeinx 反向代理 localhost:8000 直接跳转到 http://localhost:3000
  
  ```text
  server {
        listen       8000;
        #listen       somename:8080;
        server_name  somename  alias  another.alias;

        location / {
            proxy_pass   http://localhost:3000;
            index  index.html index.htm;
        }
    }
  ```
##### listen
配置网络监听，主要语法结构有三种
  1. 配置监听的IP地址
  ```
  listen address[:port] [default_server] [setfib=number] [backlog=number] [rcvbuf=size] [sndbuf=size] [deferred] [accept_filter=filter] [bind] [ssl];
  ```
  2. 配置监听端口
  ```
  listen port[default_server] [setfib=number] [backlog=number] [rcvbuf=size] [sndbuf=size] [accept_filter=filter] [deferred] [bind] [ipv6only=on|off] [ssl];
  ```
  3. 配置 UNIX Domain Socket
  ```
  listen unix:path [default_server]  [backlog=number] [rcvbuf=size] [sndbuf=size] [accept_filter=filter] [deferred] [bind] [ssl];
  ```
- 实例
```text
listen *:80 | *:8080  #监听所有80端口和8080端口
listen  IP_address:port   #监听指定的地址和端口号
listen  IP_address     #监听指定ip地址所有端口
listen port     #监听该端口的所有IP连接
```

- 下面分别解释每个选项的具体含义：
> `address` : IP地址，如果是 IPV6地址，需要使用中括号[] 括起来，比如[fe80::1]等。
> `address` : IP地址，如果是 IPV6地址，需要使用中括号[] 括起来，比如[fe80::1]等。
> `port` : 端口号，如果只定义了IP地址，没有定义端口号，那么就使用80端口。
> `path` : socket文件路径，如 var/run/nginx.sock等。
> `default_server` : 标识符，将此虚拟主机设置为 address:port 的默认主机。（在 nginx-0.8.21 之前使用的是 default 指令）
> `setfib` = number : Nginx-0.8.44 中使用这个变量监听 socket 关联路由表，目前只对 FreeBSD 起作用，不常用。
> `backlog` = number : 设置监听函数listen()最多允许多少网络连接同时处于挂起状态，在 FreeBSD 中默认为 -1,其他平台默认为511.
> `rcvbuf` = size : 设置监听socket接收缓存区大小。
> `sndbuf` = size : 设置监听socket发送缓存区大小。
> `deferred` : 标识符，将accept()设置为Deferred模式。
> `accept_filter = filter` : 设置监听端口对所有请求进行过滤，被过滤的内容不能被接收和处理，本指令只在 FreeBSD 和 NetBSD 5.0+ 平台下有效。filter 可以设置为 dataready 或 httpready 。
> `bind` : 标识符，使用独立的bind() 处理此address:port，一般情况下，对于端口相同而IP地址不同的多个连接，Nginx 服务器将只使用一个监听指令，并使用 bind() 处理端口相同的所有连接。
> `ssl` : 标识符，设置会话连接使用 SSL模式进行，此标识符和Nginx服务器提供的 HTTPS 服务有关。

##### server_name
- 用于虚拟主机的配置，通常两种配置方法：
```
server_name name...;
```
  1. 对于 name 来说，可以只有一个名称，也可以有多个名称，中间用空格隔开。而每个名字由两段或者三段组成，每段之间用 `.` 隔开
  ```text
  server_name baidu.com www.baidu.com
  ```
  2. 可以使用通配符 `*`，但通配符只能用在由 ***三段字符*** 诸城的首段或者尾端
  ```text
  server_name baidu.com www.baidu.com
  ```
  3. 可以使用正则表达式，用“~”作为正则表达式字符串的开始标记
  ```text
  server_name ~^www\d+\.123\.com$;
  ```
  
  - 匹配优先级
  
  ```text
  1. 准确匹配 server_name
  2. 通配符在开始时匹配 server_name 成功
  3. 通配符在结尾时匹配 server_name 成功
  4. 正则表达式匹配 server_name 成功
  ```
- 基于 IP 地址的虚拟主机配置
语法结构和基于域名匹配一样，而且不需要考虑通配符和正则表达式的问题。
```text
server_name 192.168.1.1
```

##### location
用于匹配 URL
```text
 location [ = | ~ | ~* | ^~] uri { 
}
```

1. `=` ：用于不含正则表达式的 uri 前，要求请求字符串与 uri 严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求。
2. `~` ：用于表示 uri 包含正则表达式，并且区分大小写。
3. `~*` ：用于表示 uri 包含正则表达式，并且不区分大小写。
4. `^~` ：用于不含正则表达式的 uri 前，要求 Nginx 服务器找到标识 uri 和请求字符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location 块中的正则 uri 和请求字符串做匹配。
> 注意：如果 uri 包含正则表达式，则必须要有 ~ 或者 ~* 标识

##### proxy_pass
设置代理服务器的地址,可以是 主机名称、IP 地址加端口，URI 等
```text
prox_pass URL
```

## 配置图解
```text

#user nobody; # user 指定运行 nginx 的用户和组（第一个参数为用户第二个为组，这里只有用户）
worker_processes  1; #运行时使用CPU的个数

#error_log  logs/error.log; # 指定错误日志为 logs/ 目录下的 error.log 文件
#error_log  logs/error.log  notice; # 指定错误日志，并指定写入格式为 notice
#error_log  logs/error.log  info; # 指定错误日志，并指定写入格式为 info

#pid        logs/nginx.pid; # 指定 pid 文件（存放主进程 pid 号）


events {
    worker_connections  1024; #一个woeker进程的最大连接数
}

#Nginx用作虚拟主机时使用。每一个server模块生成一个虚拟主机。
http {
    include       mime.types; #定义MIME类型和后缀名关联的文件的位置
    default_type  application/octet-stream; #指定mime.types文件中没有记述到的后缀名的处理方法

    # 写入格式 main 的内容格式如下
    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main; # 指定访问日志和写入格式为 main

    sendfile        on;  #是 否使用OS的sendfile函数来传输文件
    #tcp_nopush     on; # 启用或者禁用使用套接字选项（仅在 sendfile 使用时使用）

    #keepalive_timeout  0;  # 0 值禁用保持活动的客户端连接
    keepalive_timeout  65;  #HTTP连接的持续时间。设的太长会使无用的线程变的太多

    #gzip  on; # 启用或者禁用 gzip

    # 虚拟主机配置模块
    server {
        listen       8888; #监听端口

        server_name  localhost; #服务地址

        charset utf-8;   #编码方式
        #charset koi8-r;

        #root /var/www; #你的网站根目录
        root  /Users/b/Desktop;

        error_log  logs/error.log;
        #access_log  logs/host.access.log  main;
        location  /a/ {
            alias /Users/b/Desktop/Nginx_files/; # 别名
            index  6.gif IMG_2021.jpg;  # 定义索引，按顺序匹配
        }
        #若按照上述配置的话，则访问/a/目录里面的文件时，ningx会自动去/Users/b/Desktop/Nginx_files/目录找文件

        # 将特定的文件或目录重新定位，如 php 文件，image 目录等
        location /Nginx_files/ {
            root  /Users/b/Desktop;
            index  6.gif IMG_2021.jpg;  # 定义索引，按顺序匹配
        }
        #若按照这种配置的话，则访问/Nginx_files/目录下的文件时，nginx会去/Users/b/Desktop/Nginx_files/目录下找文件。
        error_page  404              /404.html;  # 定义显示 404 错误的 uri

        # redirect server error pages to the static page /50x.html
        #

        error_page   500 502 503 504  /50x.html;

        location = /50x.html {   # location 精准匹配 '/50x.html'
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {  # 正则表达式: .php文件走的路径
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;  #走fastcgi 路径
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name; #定义的根目录 以及 请求的脚名
        #    include        fastcgi_params; # 请求参数
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
    include servers/*;
}

```

## 参考文档
- 苗泽老师的《Nginx高性能Web服务器详解》

