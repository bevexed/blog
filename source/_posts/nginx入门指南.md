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
  - event和http指令驻留在主上下文中，server驻留在http中，location驻留在server中。
  - 一行中在#符号后面的是注释

## 功能
> web服务器最重要的任务是对外提供文件，例如(图片或者静态HTML网页)。

### 提供静态资源内容
- 通常，配置文件包含几个server块指令，这些块指令通过不同的服务名字（server names）分发监听（distinguished listen）在不同的端口。
- 一旦nginx决定哪个服务器处理请求，它就会根据server块指令中定义的location指令的参数匹配请求头中指定的URI。
- 在server块指令中添加location指令：
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

## 配置图解
````

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

    sendfile        on;  #是否使用OS的sendfile函数来传输文件
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

    

