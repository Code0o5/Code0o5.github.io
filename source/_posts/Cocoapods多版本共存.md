---
title: Cocoapods多版本共存
copyright: true
date: 2021-05-06 17:41:50
categories: [iOS, CocoaPods]
tags: [iOS]
---



## 1. 前言

`Cocoapods`在`1.8`版本以后加入了`CDN`，加快了下载第三方库文件的速度。所以有的时候可能会同时需要`1.8`前和`1.8`后版本的`Cocoapods`。



***

## 2. 查看`Cocoapods`版本

### 2.1 查看本地所有版本的`Cocoapods`

```
gem list cocoapods
```

结果如下：

```

*** LOCAL GEMS ***

cocoapods (1.10.1)
cocoapods-core (1.10.1)
cocoapods-deintegrate (1.0.4)
cocoapods-downloader (1.4.0)
cocoapods-plugins (1.0.0)
cocoapods-search (1.0.0)
cocoapods-trunk (1.5.0)
cocoapods-try (1.2.0)
```

本地安装了最新的`1.10.1`版本。



### 2.2 安装其他版本

安装`1.6.2`版本

```
sudo gem install cocoapods -v 1.6.2
```

再次查看本地安装的所有版本：

```

*** LOCAL GEMS ***

cocoapods (1.10.1, 1.6.2)
cocoapods-core (1.10.1, 1.6.2)
cocoapods-deintegrate (1.0.4)
cocoapods-downloader (1.4.0)
cocoapods-plugins (1.0.0)
cocoapods-search (1.0.0)
cocoapods-stats (1.1.0)
cocoapods-trunk (1.5.0)
cocoapods-try (1.2.0)
```



### 2.3 查看当前使用的`Cocoapods`版本

```
pod --version
```

结果:

```
1.10.1
```

这是因为默认使用最新的版本。



### 2.4 使用指定版本的`Cocoapods`

可以在执行`pod`命令时指定版本号，如：

```
pod _1.6.2_ install
```

