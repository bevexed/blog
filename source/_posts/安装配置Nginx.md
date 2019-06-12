---
title: ubuntu 18.04 安装配置Nginx
date: 2019-01-29 11:44:14
categories: 
  - 服务器
tags: 
  - Nginx
---
## 安装
```bash
> apt install nginx
```

## 配置防火墙
```bash
> sudo ufw app list
```
输入命令后，你会看到
```bash
Available applications:
  Nginx Full 这个配置打开 80端口和443端口
  Nginx HTTP 这个配置只打开80 (普通, 未加密通信)
  Nginx HTTPS  这个配置只打开 443 (TLS/SSL 加密通信)
  OpenSSH
```

## 打开服务
```bash
> sudo ufw allow 'Nginx Full'
```

## 查看服务是否打开
### 验证服务状态
```bash
> sudo ufw status
```

### 检查 web server
```bash
> systemctl status nginx
```
### 通过 IP 访问
输入你的网站IP即可
![nginx](nginx.png)

## 管理 Nginx进程
### 停止
```bash
> sudo systemctl stop nginx
```
### 启动
```bash
> sudo systemctl start nginx
```
### 重启
```bash
> sduo systemctl restart nginx
```
### 开机启动
```bash
> sudo systemctl enable nginx
```
### 禁止开机启动
```bash
> sudo systemctl disable nginx
```

## 配置
### 网站文件位置
> /var/www/html
#### 修改网站根目录
- 进入Nginx配置文件
```bash
> sudo vim /etc/nginx/sites-available/default
```
- 将 `root` 所对应网站根目录进行修改 成你的目录
![root](root.jpeg)
- 重启`nginx`
```bash
> sudo systemctl restart nginx
```


### 服务器配置
#### 配置文件目录
- /etc/nginx: nginx
#### 主配置文件
- /etc/nginx/nginx.conf


## 参考文章
http://www.nginx.cn/4723.html
