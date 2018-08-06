---
title: nginx
date: 2017-05-24 19:22:04
tags:
  - 开发
---

# Nginx

地址：https://nginx.org/en/

## 安装包安装

### 安装Nginx

    $sudo apt-get install nginx

Ubuntu安装之后的文件结构大致为：

所有的配置文件都在/etc/nginx下，并且每个虚拟主机已经安排在了/etc/nginx/sites-available下
程序文件在/usr/sbin/nginx
日志放在了/var/log/nginx中
并已经在/etc/init.d/下创建了启动脚本nginx
默认的虚拟主机的目录设置在了/var/www/nginx-default (有的版本 默认的虚拟主机的目录设置在了/var/www, 请参考/etc/nginx/sites-available里的配置)

### 启动Nginx

    $sudo /etc/init.d/nginx start

然后就可以访问了，http://localhost/ ， 一切正常！
如果不能访问，先不要继续，看看是什么原因，解决之后再继续。
启动时候若显示端口80被占用： Starting nginx: [emerg]: bind() to 0.0.0.0:80 failed (98: Address already in use)，修改文件：/etc/nginx/sites-available/default,去掉 listen 前面的 # 号 , # 号在该文件里是注释的意思 , 并且把 listen 后面的 80 端口号改为自己的端口，访问是需要添加端口号。（安装完后如出现403错误，那可能是nginx配置文件里的网站路径不正确）

## 配置过程中用到的命令

查看端口占用情况

    lsof -i:80 //查看80端口占用情况

杀死进程

    kill -9 3274 //3274为进程PID

常用linux命令说明

**tar**

- z用gzip对存档压缩或解压
- x-从存档展开文件
- v-详细显示处理的文件
- f-指定存档或设备 

举例：

    tar -zxvf nginx-xxx.tar.gz

**chmod**

- u:与文件属主拥有一样的权限
- +:增加权限
- rwx:可读可写可执行
- -R:递归所有目录和文件

举例：

    sudo chmod a+rwx -R logs

