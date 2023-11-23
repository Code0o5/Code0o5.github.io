---
title: Trojan服务端搭建教程
copyright: true
date: 2021-06-22 11:43:30
categories: [经验分享, 科学上网]
tags: [科学上网]
---



## 1. 检查`SELinux`状态

`SELinux`开启时容易导致证书申请失败，所以要保证为关闭状态。

- 查看`SELinux`状态

  ```
  /usr/sbin/sestatus -v
  ```

- 如果返回`enabled`表示开启，修改`/etc/selinux/config`文件，将`SELINUX=enforcing`改为`SELINUX=disabled`

- 重启VPS



***

## 2. 安装依赖环境

`CentOS`系统：

```
yum -y install wget
```



`Debian`系统：

```
apt-get install wget
```





***

## 3. 安装`trojan`

- 第一步，安装相关服务，绑定域名

  ```
  wget -N --no-check-certificate "https://raw.githubusercontent.com/V2RaySSR/Trojansh/master/trojan1.sh" && chmod +x trojan1.sh && ./trojan1.sh
  ```

  中途提示处输入绑定VPS的域名，自动安装Ngix并下载伪站点。

- 第二步，自动申请`SSL`证书，如果证书申请失败，请按照提示手动申请证书

  ```
  wget -N --no-check-certificate "https://raw.githubusercontent.com/V2RaySSR/Trojansh/master/trojan2.sh" && chmod +x trojan2.sh && ./trojan2.sh
  ```

- 第三步

  ```
  wget -N --no-check-certificate "https://raw.githubusercontent.com/V2RaySSR/Trojansh/master/trojan3.sh" && chmod +x trojan3.sh && ./trojan3.sh
  ```

- trojan配置文件位置：`/usr/local/etc/trojan/config.json`

- 重启trojan

  ```
  systemctl restart trojan
  ```



***

## 4. 安装BBR加速

- 下载脚本

  ```
  wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh"
  ```

- 添加可执行权限

  ```
  chmod +x tcp.sh
  ```

- 安装BBR魔改版

  ```
  echo -e "1" | ./tcp.sh
  ```

  安装完成提示重启VPS

- 开启BBR魔改版加速

  ```
  echo -e "5" | ./tcp.sh
  ```

  