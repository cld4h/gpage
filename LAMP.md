---
title: LAMP on Archlinux
date: 2019-10-08 17:03:28
tags:
---

[参考](https://www.linode.com/docs/web-servers/lamp/how-to-install-a-lamp-stack-on-arch-linux/)
## Apache

### 安装及配置

```sh
sudo pacman -Syu apache
```

编辑 `/etc/httpd/conf/extra/` 目录下的 `httpd-mpm.conf` 配置文件以调整资源使用配置。

```conf
# prefork MPM
# StartServers: number of server processes to start
# MinSpareServers: minimum number of server processes which are kept spare
# MaxSpareServers: maximum number of server processes which are kept spare
# MaxRequestWorkers: maximum number of server processes allowed to start
# MaxConnectionsPerChild: maximum number of connections a server process serves
#                         before terminating
<IfModule mpm_prefork_module>
    StartServers             5
    MinSpareServers          5
    MaxSpareServers         10
    MaxRequestWorkers      250
    MaxConnectionsPerChild   0
</IfModule>
```

编辑 `httpd-default.conf` 文件关闭 KeepAlive

```conf
KeepAlive Off
```

启动 Apache 服务

```sh
sudo systemctl enable httpd.service
```

### 添加基于域名的Virtual Host

打开 `httpd.conf` ，编辑 `DocumentRoot /srv/http`来定义默认文档根，
```conf
DocumentRoot "/srv/http/default"
```

对下面一行取消注释
```conf
Include conf/extra/httpd-vhosts.conf
```

打开`extra` 目录下的 `httpd-vhosts.conf`
类似下面编辑示例 virtual hosts 块
将 `example.com` 替换成自己的域名

```conf
<VirtualHost *:80>
    ServerAdmin 13521592970@126.com
    DocumentRoot "/srv/http/localhost/public_html"
    ServerName localhost
    ServerAlias localhost
    ErrorLog "/srv/http/localhost/logs/error.log"
    CustomLog "/srv/http/localhost/logs/access.log" combined
        <Directory />
            Order deny,allow
            Allow from all
        </Directory>
</VirtualHost>
```

删除文件中的第二个示例,或用其配置第二个网站。

创建文件夹
```sh
sudo mkdir -p /srv/http/default
sudo mkdir -p /srv/http/localhost/public_html
sudo mkdir -p /srv/http/localhost/logs
```

## MariaDB

```sh
sudo pacman -Syu mariadb  mariadb-clients libmariadbclient
sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
sudo systemctl start mysqld.service
sudo systemctl enable mysqld.service
mysql_secure_installation
```

## PHP

```sh
sudo pacman -Syu php php-apache
```

php 主配置文件见 `/etc/php/php.ini`

在其中取消 `extension=pdo_mysql` 和 `extension=mysqli` 的注释

创建日志目录并修改所有者为Apache服务器
```sh
sudo mkdir /var/log/php
sudo chown http /var/log/php
```

在 `/etc/httpd/conf/httpd.conf` 适当位置中加入下面内容
```conf
# Dynamic Shared Object (DSO) Support
LoadModule php7_module modules/libphp7.so
AddHandler php7-script php

# Supplemental configuration
# PHP 7
Include conf/extra/php7_module.conf

# Located in the <IfModule mime_module>
AddType application/x-httpd-php .php
AddType application/x-httpd-php-source .phps
```

将`/etc/httpd/conf/httpd.conf` 的
```conf
LoadModule mpm_event_module modules/mod_mpm_event.so
```
注释掉，换成
```conf
LoadModule mpm_prefork_module modules/mod_mpm_prefork.so
```

## DVWA 配置

安装 php-gd

```sh
sudo pacman -S php-gd
```
在`/etc/php/php.ini`中取消 `extention=gd` 的注释

```sh
cd /srv/http/localhost
sudo git clone https://github.com/ethicalhack3r/DVWA
systemctl restart httpd
```
allow_url_include = On
allow_url_fopen = On ;default

## demo login page

### 创建数据库和表
参考：
https://www.tutorialrepublic.com/php-tutorial/php-mysql-login-system.php
http://transcoders3.blogspot.com/2012/01/creating-functional-login-page-in-php.html

```sql
CREATE database demo;
use demo;
CREATE TABLE users (
    id INT NOT NULL PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

CREATE user demo@localhost identified by "very-strong-passwd-for-demo";
grant all privileges on demo.* to demo@localhost identified by "very-strong-passwd-for-demo";
flush privileges;
# create table login ( id int PRIMARY KEY, username varchar(20), password varbinary(20) );
```
[Maria DB Knowledge Base](https://mariadb.com/kb/en/library/create-table/)

输入数据
```sql

```
