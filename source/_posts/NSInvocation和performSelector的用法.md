---
title: NSInvocation和performSelector的用法
copyright: true
date: 2021-07-13 11:37:48
categories: [iOS,OC,基础知识]
tags: [OC,NSInvocation]
---



## 1. 简介

`NSInvocation`和`performSelector`是`OC`中不直接调用方法的两种方式。与通过对象直接调用方法不同的是，直接调用方法需要在编译前声明方法，否则编译器会报错；而`NSInvocation`和`performSelector`不需要，这也是`runtime`的一种应用方式。

`NSInvocation`是`OC`的一个类，该类保存了方法所属的对象/方法名称/参数/返回值，可以说一个`NSInvocation`对象就是一个方法。和方法签名类`NSMethodSignature`共同完成方法的调用。

`performSelector`是`NSObject`类的一个实例方法，`OC`中的对象最终都继承自`NSObject`类，所以所有的对象都能通过该方法来调用指定的方法。通常先用`respondsToSelector`判断方法存在，然后在用`performSelector`调用。

两者区别：

- `performSelector:withObject`，完成简单的消息调用，对于参数个数大于2或者有返回值的方法，就不太好处理
- `NSInvocation`，对参数个数没有限制，可以处理多个参数和有返回值的方法调用



***

## 2. 基本使用



### 2.1 `performSelector`

根据参数个数使用指定以`performSelector`开头的方法，返回值均可以可无，如果有返回值`performSelector`方法将其返回。

- 调用没有参数，没有返回值的方法

  ```
  - (id)performSelector:(SEL)aSelector;
  ```

- 调用有一个参数的方法

  ```
  - (id)performSelector:(SEL)aSelector withObject:(id)object;
  ```

- 调用有两个参数的方法

  ```
  - (id)performSelector:(SEL)aSelector withObject:(id)object1 withObject:(id)object2;
  ```





### 2.2 `NSInvocation`

```
// 方法签名(方法的描述)
NSMethodSignature *signature = [[Weakself class] instanceMethodSignatureForSelector:@selector(test3:par2:par3:)];
if (signature == nil) {
      // 方法不存在，提示或抛出异常
      NSLog(@"%@方法不存在",NSStringFromSelector(@selector(test3:par2:par3:)));
      return;
  }

  // NSInvocation : 利用一个NSInvocation对象包装一次方法调用（方法调用者、方法名、方法参数、方法返回值）
  NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:signature];
  // 设置方法调用者
  invocation.target = Weakself;
  // 方法名，一定要和方法签名类中的一致
  invocation.selector = @selector(test3:par2:par3:);

  // 设置参数,默认已经有self和_cmd两个参数了
  NSString *par1 = @"参数1";
  NSString *par2 = @"参数2";
  NSString *par3 = @"参数3";
  [invocation setArgument:&par1 atIndex:2];
  [invocation setArgument:&par2 atIndex:3];
  [invocation setArgument:&par3 atIndex:4];

  // 调用方法
  [invocation invoke];

  // 获取返回值
 id returnValue = nil;
  // 有返回值类型，才去获得返回值
 if (signature.methodReturnLength) {
     [invocation getReturnValue:&returnValue];
     NSLog(@"返回值:%@",returnValue);
 }
```



附：[源码下载](https://github.com/Code0o5/NSInvocationDemo)

