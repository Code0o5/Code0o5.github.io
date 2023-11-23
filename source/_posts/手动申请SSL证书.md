---
title: 手动申请SSL证书
copyright: true
date: 2021-06-22 11:43:02
categories: [经验分享, 科学上网]
tags: [科学上网, SSL证书]
---



## 1. 下载`KeyManager`

- 前往[KeyManager](https://keymanager.org/)下载指定版本的安装包
- 安装，设置主密码
- 后续申请证书要用到该软件



***

## 2. 申请免费`SSL`证书

- 前往[FreeSSL](https://freessl.cn/)，输入域名，并选择指定的证书提供商，点击创建免费的SSL证书

  ![](http://res.yycode.top:8001/backup/blogImages/手动申请SSL证书/QQ20210622-120921@2x.png)

- 输入邮箱（证书将要到期会邮件通知），选择文件校验，点击创建

  ![](http://res.yycode.top:8001/backup/blogImages/手动申请SSL证书/QQ20210622-122041@2x.png)

- 提示打开`KeyManager`，点击打开。打开`KeyManager`会提示`CSR生成成功，请返回浏览器继续操作`

- 返回浏览器点击弹窗的`继续`，开始文件校验

  ![](http://res.yycode.top:8001/backup/blogImages/手动申请SSL证书/QQ20210622-122440@2x.png)

- 下载文件到指定的路径，保证完整的文件路径可以在浏览器中被访问

  ![](http://res.yycode.top:8001/backup/blogImages/手动申请SSL证书/QQ20210622-122736@2x.png)

- 然后点击配置完成，检测一下。等待检测结果，只需要有一个匹配即可

  ![](http://res.yycode.top:8001/backup/blogImages/手动申请SSL证书/QQ20210622-123208@2x.png)

- 回到上一步，点击验证，验证完成，回到`KeyManager`下载证书

