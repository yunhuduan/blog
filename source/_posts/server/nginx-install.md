---
title: nginx安装
date: 2017-10-25 23:34:13
tags: 
	- httpServer
categories:
	- server
	- nginx
---
### 官网下载
1.直接下载.tar.gz安装包，地址：https://nginx.org/en/download.html

2.使用wget命令下载(推荐)
```shell
wget -c https://nginx.org/download/nginx-1.12.2.tar.gz
```
### 解压包(先移动到需要安装的目录)
```shell
tar -xzvf nginx-1.12.2.tar.gz
cd nginx-1.12.2
```
### 配置
```shell
./configure
```
以上命令执行后会坚持当前环境的依赖,如果有未安装的包请安装好第4步骤中执行安装

<!-- more -->

### 依赖环境包安装

- gcc 安装
安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境，如果没有 gcc 环境，则需要安装：

```shell
yum install gcc-c++
```

- PCRE pcre-devel 安装
PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。nginx也需要此库。命令：

```shell
yum install -y pcre pcre-devel
```

- zlib 安装
zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip ，所以需要在 Centos 上安装 zlib 库。

```shell
yum install -y zlib zlib-devel
```

- OpenSSL 安装
OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及 SSL 协议，并提供丰富的应用程序供测试或其它目的使用。
nginx 不仅支持 http 协议，还支持 https（即在ssl协议上传输http），所以需要在 Centos 安装 OpenSSL 库。

```shell
yum install -y openssl openssl-devel
```
### 编译安装
```shell
make
make install
```
### 检测安装是否成功
1.找到Nginx安装目录
```shell
whereis nginx
```
> nginx: /usr/local/nginx
2.执行命令
```shell
sh /usr/local/nginx/sbin/nginx -v
```
### 启动Nginx
```shell
cd /usr/local/nginx
./sbin/nginx
```
### nginx常用命令
./nginx 

./nginx -s stop

./nginx -s quit

./nginx -s reload

./nginx -s quit:此方式停止步骤是待nginx进程处理任务完毕进行停止。

./nginx -s stop:此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程。

查询nginx进程：
```shell
ps aux|grep nginx
```
nginx具体代理配置等下一章详细再记录