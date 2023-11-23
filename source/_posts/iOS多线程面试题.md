---
title: iOS多线程面试题
copyright: true
date: 2021-06-30 16:39:17
categories: [iOS, OC, 面试题]
tags: [OC, 面试题]
---



## 1. 什么是GCD ？谈谈你对GCD的理解？

- 答案

  >GCD是Grand Central Dispatch的缩写。
  >
  >Grand Central Dispatch (GCD)是Apple开发的一个多核编程的较新的解决方法。在Mac OS X 10.6雪豹中首次推出，并在最近引入到了iOS4.0。
  >
  >GCD是一个替代诸如NSThread等技术的很高效和强大的技术。GCD完全可以处理诸如数据锁定和资源泄漏等复杂的异步编程问题。

- 简单使用，异步执行任务

  ```
  // 创建一个队列
      dispatch_queue_t myQueue =  dispatch_queue_create("com.iphonedevblog.post",  NULL);
      // 异步执行任务
      dispatch_async(myQueue, ^{
          NSLog(@"线程:%@",[NSThread currentThread]);
          
          // 回到主队列更新界面
          dispatch_async(dispatch_get_main_queue(), ^{
              self.view.backgroundColor = [UIColor redColor];
          });
      });
  ```

  结果：

  > **线程:<NSThread: 0x6000024b1180>{number = 7, name = (null)}**

- 线程的暂停和恢复（只能在子线程，主线程不起作用）

  ```
  dispatch_suspend(<#dispatch_object_t  _Nonnull object#>) // 暂停线程
  dispatch_resume(<#dispatch_object_t  _Nonnull object#>)  // 恢复线程
  ```



