---
title: cocoapods公有库制作
copyright: true
date: 2021-04-19 21:58:35
categories: [iOS, CocoaPods]
tags: [iOS]
---



## 1. `Pod`库源码制作

### 1.1 下载`Cocoapods`模板

执行如下命令，创建一个`Pod`库项目：

```
pod lib create pod库名
```

根据提示填写库的信息：

```
➜  测试demo  pod lib create HYTestPods
Cloning `https://github.com/CocoaPods/pod-template.git` into `HYTestPods`.
Configuring HYTestPods template.

------------------------------

To get you started we need to ask a few questions, this should only take a minute.

If this is your first time we recommend running through with the guide:
 - https://guides.cocoapods.org/making/using-pod-lib-create.html
 ( hold cmd and click links to open in a browser. )


What platform do you want to use?? [ iOS / macOS ]
 > iOS

What language do you want to use?? [ Swift / ObjC ]
 > ObjC

Would you like to include a demo application with your library? [ Yes / No ]
 > Yes

Which testing frameworks will you use? [ Specta / Kiwi / None ]
 >
specta
Would you like to do view based testing? [ Yes / No ]
 > No

What is your class prefix?
 > HY
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint:
hint: 	git config --global init.defaultBranch <name>
hint:
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint:
hint: 	git branch -m <name>

Running pod install on your new library.

Analyzing dependencies
Downloading dependencies
Installing Expecta (1.0.6)
Installing HYTestPods (0.1.0)
Installing Specta (1.0.7)
Generating Pods project
Integrating client project

[!] Please close any current Xcode sessions and use `HYTestPods.xcworkspace` for this project from now on.
Pod installation complete! There are 3 dependencies from the Podfile and 3 total pods installed.

 Ace! you're ready to go!
 We will start you off by opening your project in Xcode
  open 'HYTestPods/Example/HYTestPods.xcworkspace'

To learn more about the template see `https://github.com/CocoaPods/pod-template.git`.
To learn more about creating a new pod, see `https://guides.cocoapods.org/making/making-a-cocoapod`.
```

执行完成会自动创建指定名称的`Pod`库目录，并自动打开示例项目。`Pod`库目录结构如下：

<img src="http://res.yycode.top:8001/backup/blogImages/iOS制作私有Pod库/QQ20210419-223908@2x.png" style="zoom:50%;" />

1. `Example`：示例代码
2. `HYTestPod`：`Classess`源代码，`Assets`资源文件
3. `HYTestPod.podspec`：记录库的信息



### 1.2 完善`Pod`配置文件

在`GitHud`创建私有库，修改`Pod`库配置文件`[YourPodName].podspec`：

```
Pod::Spec.new do |s|

  # 库名称
  s.name             = 'HYFramework'

  # 指定支持的平台和版本，不写则默认支持所有的平台，如果支持多个平台，则使用下面的deployment_target定义
  #spec.platform      = :ios

  # 版本号
  s.version          = '1.0.0'

  # 库简短介
  s.summary          = '自定义OC开发工具集'

# This description is used to generate tags and improve search results.
#   * Think: What does it do? Why did you write it? What is the focus?
#   * Try to keep it short, snappy and to the point.
#   * Write the description between the DESC delimiters below.
#   * Finally, don't worry about the indent, CocoaPods strips it!

  # 开源库描述
  s.description      = <<-DESC
一套自定义的OC开发工具集
                       DESC

  # 开源库地址，或者是博客、社交地址等
  s.homepage         = 'https://github.com/MrChenYoung'

  # s.screenshots     = 'www.example.com/screenshots_1', 'www.example.com/screenshots_2'
  
  # 开源协议
  s.license          = { :type => 'MIT', :file => 'LICENSE' }

  # 开源库作者
  s.author           = { 'mrchenyoung' => 'chenhuiyiyoung@163.com' }

  # 开源库GitHub的路径与tag值，GitHub路径后必须有.git,tag实际就是上面的版本
  s.source           = { :git => 'https://github.com/mrchenyoung/HYFramework.git', :tag => s.version.to_s }

  # 社交网址
  # s.social_media_url = 'https://twitter.com/<TWITTER_USERNAME>'


  # 开源库最低支持
  s.ios.deployment_target = '9.0'

  # 源库资源文件
  s.source_files = 'HYFramework/Classes/**/*'
  
  # 是否支持arc
  s.requires_arc = true

  # s.resource_bundles = {
  #   'HYFramework' => ['HYFramework/Assets/*.png']
  # }

  # s.public_header_files = 'Pod/Classes/**/*.h'

  # 依赖系统库
  # s.frameworks = 'UIKit', 'MapKit'
  
  # 开源库依赖库
  s.dependency 'AFNetworking', '3.2.1'
  s.dependency 'Masonry'
  s.dependency 'MBProgressHUD'
  s.dependency 'MJRefresh'
  s.dependency 'YYModel'
  s.dependency 'ZLPhotoBrowser'
  
end
```



### 1.3 源码编写

所有的`pod`源码都存放在`HYTestPod`目录下的`Classes`目录下。示例代码中`Pod`下有一个`Development Pods`文件夹，该目录下的`HYTestPod`目录和`pod`库根文件夹下的同名目录是相关联的，保证了在示例项目中修改的源码和`pod`库根目录下同名文件夹下的源码保持一致。

<img src="http://res.yycode.top:8001/backup/blogImages/iOS制作私有Pod库/QQ20210419-224440@2x.png" style="zoom:50%;" />





### 1.4 `Pod`配置文件验证

本地`pod`库配置文件验证，适用于还没有把源码提交到`github`前，本地配置的验证（如果关联有其他库，需要带`--use-libraries`参数）：

```
pod lib lint --allow-warnings
```



远端`pod`库配置文件的验证，验证`github`上库的配置文件：

```
pod spec lint --allow-warnings
```



如果有如下提示，添加`--allow-warnings`参数忽略警告：

![](http://res.yycode.top:8001/backup/blogImages/iOS制作私有Pod库/QQ20210419-225830@2x.png)

提示`HYTestPod passed validation.`表示验证通过。





***

## 2. 提交代码到`GitHub`

在`github`创建放`pod`库源码的代码库，注意`pod`库配置文件的`s.source`内的地址要和该库的地址保持一致。然后在`pod`库的根目录下执行如下命令，提交源码到`github`：

```
#添加远端地址
git remote add origin https://github.com/MrChenYoung/HYTestPod.git  
#提交所有内容
git add .  && git commit -m 'v1.0.0'
#推送代码到Github
git push origin master  
# 添加 1.0.0 tag
git tag 1.0.0  
#提交tag
git push origin 1.0.0 
```

现在就可以是在项目`podfile`文件中添加库信息，执行`pod install`就可以使用了：

```
pod 'HYTestPod', :git => 'https://github.com/MrChenYoung/HYTestPods.git'
```

但是还不能够通过`pod search`命令搜索到库，而且只有知道`pod`库的`git`地址才能够使用。





***

## 3. 发布`podspec`配置文件到公有`pod`库

需要把`pod`库的配置文件(.podspec文件)提交到`cocoapods`的公有管理库，也就是`https://github.com/CocoaPods/Specs.git`上，才可以通过`pod search`命令查找到库，并且只要在项目的`podfile`文件中添加`pod  库名`就可以安装。



### 3.1 注册`session`

如果是第一次发布`podspec`文件，会提示`[!] You need to run `pod trunk register` to register a session first.`，表示要先注册`session`:

```
pod trunk register chenhuiyiyoung@163.com 'code009' --description='A description'
```

- `chenhuiyiyoung@163.com`是真正的邮箱，用于注册验证
- `code009`为用户名
- `A description`描述文本

执行完注册命令，会收到一封验证的邮件，复制邮件中的链接打开验证。通过一下命令查看自己的注册信息：

```
pod trunk me
```

给`pod`库添加拥有者：

```
pod trunk add-owner [POD REP NAME] [NEW EMAIL]
```

从`pod`库拥有者列表中移除用户：

```
pod trunk remove-owner [POD REP NAME] [OLD EMAIL]
```





### 3.2 发布`podspec`

发布`podspec`文件（如果关联有其他库，需要带`--use-libraries`参数）：

```
pod trunk push HYTestPods.podspec --allow-warnings
```

有如下提示，表示发布成功：

```
Updating spec repo `trunk`

--------------------------------------------------------------------------------
 🎉  Congrats

 🚀  HYTestPods (1.0.0) successfully published
 📅  May 13th, 19:57
 🌎  https://cocoapods.org/pods/HYTestPods
 👍  Tell your friends!
--------------------------------------------------------------------------------
```

现在就可以在项目的`podfile`文件中添加`pod HYTestPods`，然后执行`pod install`添加库到项目中了。如果`pod install`提示找不到库，执行`pod install --repo-update`命令。





***

## 4. `pod`库版本更新

- 更新新版本源码

- 更改`.podspec`文件中`s.version`字段为最新版本号，如`2.0.0`

- 提交代码到`github`

  ```
  git add .
  git commit -m 'new version'
  git tag 2.0.0
  git push orign master
  git push --tags
  ```

- 发布新版`.podspec`文件到`pod`公有库中（如果关联有其他库，需要带`--use-libraries`参数）

  ```
  pod trunk push HYTestPods.podspec --allow-warnings
  ```

  如果`pod search`搜索不到新版本，先执行`pod repo update`命令，然后在搜索。

