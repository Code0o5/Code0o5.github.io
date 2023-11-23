---
title: CocoaPods安装教程
copyright: true
date: 2021-04-26 15:37:43
categories: [iOS, CocoaPods]
tags: [iOS]
---



### `CocoaPods`简介

`CocoaPods`是用`Ruby`写的，用于`iOS`开发中第三方开源库的管理。安装`CocoaPods`要用到`gem`。



***



## 1. `gem`使用

### 1.1 `gem`简介

> `RubyGems`是`Ruby`的一个包管理器，提供了分发`Ruby`程序和库的标准格式“gem”，旨在方便地管理`gem`安装的工具，以及用于分发`gem`的服务器。 --维基百科
>
> 
>
> `Gem`是封装起来的`Ruby`应用程序或代码库。
>
> 注：在终端使用的gem命令，是指通过RubyGems管理Gem包。

### 1.2 `gem`安装

`Mac`自带`gem`。



### 1.3 `ruby`和`gem`常用命令

```
ruby -v #查看ruby版本
gem -v  #查看gem版本
gem update --system #更新RubyGems软件自身
gem update #更新gem安装的所有程序包
gem up date xxx #更新某个程序包

gem sources -l #列出安装源
gem sources -a XXX #添加安装源
gem sources -r XXX #删除安装源
gem sources -u #更新安装源

gem install xxx #安装xxx,从本地或远程服务器
gem install xxx --remote #安装xxx,从远程服务器
gem install xxx -v(或者--version) 1.6.2  #指定安装版本的
gem uninstall xxx #卸载xxx包,删除所有版本
gem uninstall xxx -v 1.6.2 #卸载1.6.2版本的xxx包

gem list #列出本地gem安装的所有程序包
gem list d #列出本地以d开头的程序包
gem list | grep cocoapods   #查看已安装cocoapods版本

gem search log --both #从本地和远程服务器上查找含有log字符串的包
gem search log --remoter #只从远程服务器上查找含有log字符串的包
gem search -r log #只从远程服务器上查找含有log字符串的包
```



***



## 2. 安装`ruby`

- 查看`ruby`版本

  ```
  ruby -v
  ```

  如果提示不识别的命令，标识没有安装`ruby`，需要执行以下步骤安装。

- 安装`rvm`

  ```bash
  curl -L get.rvm.io | bash -s stable 
  source ~/.bashrc
  source ~/.bash_profile
  ```

- 查看`rvm`版本

  ```
  rvm -v
  ```

- 查看可安装的`ruby`版本

  ```
  rvm list known
  ```

- 安装指定版本的`ruby`

  ```
  rvm install 2.7.0
  ```

  如果没有安装`Xcode`和`Command Line Tools for Xcode`以及`Homebrew`会自动下载安装，建议提前安装好。

- 设置默认`ruby`版本

  ```
  rvm use 2.7.0 --default
  ```

- 查看当前`ruby`源

  ```
  gem sources -l
  ```

- 更换`ruby`源，`ruby`默认的源地址`https://rubygems.org/`在国外，安装`cocoapods`可能会报错，最好切换到国内镜像源`https://gems.ruby-china.com/`

  ```
  gem sources --remove https://rubygems.org/  #移除默认源
  gem sources --add https://gems.ruby-china.com/ #添加新的源
  ```



***



## 3. 安装`CocoaPods`

- 如果安装多个`Xcode`，需要选择版本，一般需要选择最近的Xcode版本

  ```
  sudo xcode-select -switch /Applications/Xcode.app/Contents/Developer
  ```

- 安装`CocoaPods`

  ```
  sudo gem install cocoapods  #安装最新版cocoapods
  sudo gem install cocoapods -v 1.8.4  #安装指定版本的cocoapods
  sudo gem install -n /usr/local/bin  cocoapods -v 1.8.4  #安装指定版本的cocoapods
  ```
  
- 下载文件

  ```
  pod setup
  ```

  `cocoapods`在1.8版本以后加入了`CDN`,不需要下载`master`分支到`~/.cocoapods/repos`，执行该命令会直接提示Setup completed。如果是1.8之前的版本，则需要等待下载。参考[https://blog.cocoapods.org/CocoaPods-1.8.0-beta/](https://blog.cocoapods.org/CocoaPods-1.8.0-beta/)



***



## 4. `cocoapods`使用

- 在项目根目录下执行命令

  ```
  pod init
  ```

  执行完成会自动在目录下生成`Podfile`文件

- 编辑`Podfile`文件，添加第三方库

  ```
  pod 'AFNetworking'
  ```

- 安装第三方

  ```
  pod install
  ```
  
- 升级`pod`库

  ```
  pod update [pod库名称] 
  ```

  不指定`pod`库名称，默认更新`podfile`里全部库。

  `pod install命令按照podfile.lock文件中的版本安装库，不检查更新库；pod update拉取最新的库版本，并更新符合podfile文件中指定的版本条件的库。建议多使用pod install,pod update命令使用之前先使用pod outdated命令查看有哪些库有新版本，再针对指定pod库更新版本。 `



***



## 5. `Podfile`文件解析

`Podfile`是项目指定`target`的依赖规范。配置如下：

```
# 支持的iOS系统的最低版本
platform:ios,'8.0'

# 指定开源库的下载源（组件化开发时会用到）
#source 'https://github.com/CocoaPods/Specs.git'
#source 'https://code.houbank.net/xxx/xxxSpecs.git'

# ignore all warnings from all pods
inhibit_all_warnings!

# 使用framework（包含swift库就必须使用此设置，纯OC库可以不设置）
use_frameworks!

# 项目中如果有多个工程文件所以必须显式告知Cocopods，Pod的库属于哪个工程的依赖程的依赖
#project 'ProjectName.xcodeproj'

target 'ProjectName' do
    pod 'AFNetworking',               # 网络基础库    
end
```



### 5.1 项目配置

- 指定支持的系统及版本

  ```
  platform:ios,'8.0'
  ```

- 指定下载源

  ```
  source 'https://github.com/CocoaPods/Specs.git'
  ```

  不指定默认从`cocoapods`官方公共库`https://github.com/CocoaPods/Specs.git`下载。设置镜像源，下载私有库，组件化开发等会用到该项配置。

- 忽略第三方框架的警告

  ```
  inhibit_all_warnings!
  ```

- 是否使用动态库（iOS8.0以上支持）

  ```
  use_frameworks!
  ```

  - 不使用，会生成第三方框架对应的`.a`文件（静态链接库），位置：`Build Phases --> Link Binary With Libraries --> libPods-**.a`
  - 使用，会生成第三方框架对应的`frameworks`文件（包含了头文件、二进制文件、资源文件等），位置：`Build Phases --> Link Binary With Libraries --> Pods_xxx.framework`
  - 纯OC项目导入纯OC框架，一般不使用
  - swift项目导入swift框架，必须使用
  - swift项目导入OC框架，如果使用frameworks，在桥接文件中导入格式为：`\#import "AFNetworking/AFNetworking.h"`；如果不使用frameworks，在桥接文件中导入格式为：`#import "AFNetworking.h"`

- 指定是哪个工程的依赖，项目中有多个工程必须指定，单个工程一般忽略

  ```
  project 'ProjectName.xcodeproj'
  ```

  

### 5.2 第三方框架版本控制

- 版本号说明

  ```
  版本号格式：x.y.z
  x表示重大更新的迭代版本号
  y表示项目添加了一些新功能
  z表示修复版本号，一般是修复项目的bug
  ```

- 只指定三方库名称，默认使用最新版本的

  ```
  pod 'AFNetworking'
  ```

- 使用指定版本号的库

  ````
  pod 'AFNetworking', '3.1.0'   #使用3.1.0版本
  ````

- 使用`逻辑运算符`指定版本号

  ```
  ‘> 0.1’ 高于0.1的任何版本
  ‘>= 0.1’ 版本0.1或更高版本
  ‘< 0.1’ 小于0.1的任何版本
  ‘<= 0.1’ 版本0.1和任何较低版本
  ```

- 使用`乐观运算符`指定版本号

  ```
  ‘~> 0.1.2’ 版本0.1.2到版本0.2，不含0.2及以上
  ‘~> 0.1’ 版本0.1到版本1.0，不包括1.0及以上版本
  ‘~> 0’版本0及以上，这基本上和不指定版本号是相同的
  ```

- 指定远程第三方框架位置

  ```
  pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git'  #使用主分支
  pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git', :branch => 'dev'  #使用不同分支
  pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git', :tag => '3.1.1' #使用仓库的某一个tag
  pod 'Alamofire', :git => 'https://github.com/Alamofire/Alamofire.git', :commit => '0f506b1c45' #使用一个指定的提交commit
  ```

  从指定的远程地址下载第三方框架源码。

- 指定本地第三方路径

  ```
  pod 'Alamofire', :path => '~/Documents/Alamofire'
  ```

  指定的文件夹将成为`Pod`的根文件夹，并从该文件夹链接源码文件到项目中，制作`Pod`库时的示例项目就是很好的例子。需要注意的是指定的目录中必须包含`podspec`文件。



### 5.3 `cocoapods`更换源

`cocoapods`默认使用`github`源 ，在国内下载第三方库很慢，通过修改源来解决该问题。

- 查看当前源

  ```
  pod repo list
  ```

- 移除官方源

  ```
  pod repo remove master
  ```

- 低版本添加新的源

  ```
  pod repo add master https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git
  ```

  如果报错`[!] To setup the master specs repo, please run `pod setup`.`说明版本太高，不允许用该方式添加源，使用克隆方式添加源:

  ```
  git clone https://mirrors.tuna.tsinghua.edu.cn/git/CocoaPods/Specs.git ~/.cocoapods/repos/master
  ```

- 更新

  ```
  pod setup
  ```

  

***



## 6. 卸载`cocoapods`



### 6.1 查看本地`cocoapods`版本

查看本地所有版本

```
gem list | grep cocoapods
```



### 6.2 卸载所有版本

```
sudo gem uninstall cocoapods
```

如果安装多个版本，会提示选择要卸载的版本：

```
Select gem to uninstall:
 1. cocoapods-1.6.2
 2. cocoapods-1.10.1
 3. All versions
>
```



### 6.3 卸载指定版本

```
sudo gem uninstall cocoapods -v 1.6.2
```



### 6.4 卸载`cocoapods`相关的组件

查看本地所有`cocoapods`相关组件：

```
gem list --local | grep cocoapods
```

结果如下：

```
cocoapods-core (1.10.1, 1.6.2)
cocoapods-deintegrate (1.0.4)
cocoapods-downloader (1.4.0)
cocoapods-plugins (1.0.0)
cocoapods-search (1.0.0)
cocoapods-stats (1.1.0)
cocoapods-trunk (1.5.0)
cocoapods-try (1.2.0)
```

逐个卸载：

```
sudo gem uninstall cocoapods-core
sudo gem uninstall cocoapods-deintegrate
sudo gem uninstall cocoapods-downloader
...
```



***



## 7. `CocoaPods`常用命令

```
#查看当前使用的cocoapods版本
pod --version
#指定目录为pod目录,默认当前目录
pod init [目录名]																	
#安装第三方库
pod install
#更新第三方库，默认更新所有的库
pod update [第三方库名]
#查看哪些库有更新
pod outdated
#搜索指定的三方库
pod search 第三方库名
#查看pod源
pod repo list
#删除指定的源
pod repo remove 源名称
#添加源(只适用旧版本，新版本报错)
pod repo add master 源地址
```



