---
title: GCP实例创建使用
copyright: true
date: 2021-06-22 10:48:26
categories: [VPS, Linux]
tags: [VPS, GCP]
---



## 1.  创建防火墙规则

- 前往[谷歌云](https://console.cloud.google.com/)，在菜单栏找到`VPC网络->防火墙`

  ![](http://res.yycode.top:8001/backup/blogImages/GCP实例创建使用/QQ20210622-105655@2x.png)

- 点击创建防火墙规则，分别创建入站和出站规则

  ![](http://res.yycode.top:8001/backup/blogImages/GCP实例创建使用/QQ20210622-110453@2x.png)





***

## 2. 创建虚拟机实例

- 菜单栏找到`compute engine -> 虚拟机实例`

  ![](http://res.yycode.top:8001/backup/blogImages/GCP实例创建使用/QQ20210622-110630@2x.png)

- 点击创建实例，填写实例名称，选择地区和区域，机器配置选择最低配置的N1型，内存选614MB

  ![](http://res.yycode.top:8001/backup/blogImages/GCP实例创建使用/QQ20210622-111350@2x.png)

- 启动磁盘处选择系统类型，防火墙勾选允许http流量和https流量，添加入站和出站规则

  ![](http://res.yycode.top:8001/backup/blogImages/GCP实例创建使用/QQ20210622-111720@2x.png)

- 点击创建，完成实例创建



***

## 3. 实例配置

完成实例创建，还需要对虚拟机实例设置允许root登录，设置root密码才能在其他的终端通过ssh远程链接。

### 3.1 设置静态IP

谷歌云实例默认是临时IP，实例重启以后IP可能会变，需要设置成静态IP。

- 菜单栏找到`VPC网络->外部IP地址`

  ![](http://res.yycode.top:8001/backup/blogImages/GCP实例创建使用/QQ20210622-112432@2x.png)

- 找到对应实例，设置IP为静态

  ![](http://res.yycode.top:8001/backup/blogImages/GCP实例创建使用/QQ20210622-112552@2x.png)

- 设置静态IP完成



### 3.2  允许root登录配置

- 点击实例后的`SSH`链接，在弹出的窗口中允许权限

- 连接上以后，获取管理员权限

  ```
  sudo -i
  ```

- 修改`/etc/ssh/sshd_config`文件

  ```
  vi /etc/ssh/sshd_config
  ```

- 修改以下项为`yes`

  ![](http://res.yycode.top:8001/backup/blogImages/GCP实例创建使用/QQ20210622-113702@2x.png)

  ![](http://res.yycode.top:8001/backup/blogImages/GCP实例创建使用/QQ20210622-113726@2x.png)

- 修改完成，保存退出

- 设置root密码

  ```
  passwd root
  ```

- 两次输入密码，完成root密码设置

- 在谷歌云虚拟机实例列表，重启实例。然后就可以在其他终端通过root账号和密码远程登录虚拟机实例。

  



