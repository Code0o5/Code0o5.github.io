---
title: iOS定时器使用总结
copyright: true
date: 2021-06-28 14:56:22
categories: [iOS, OC, 基础知识]
tags: [iOS, OC]
---



`iOS`定时器主要有以下三种：

1. `NSTimer`
2. `CADisplayLink`
3. `dispatch_source_t`

其中`NSTimer`和`CADisplayLink`是基于`NSRunLoop`实现的，而`RunLoop`会在跑完一圈再去检查定时器是否满足规定的时间间隔，如果未达到，`RunLoop`会进入下一轮任务处理，这就可能导致定时器存在计数误差，`dispatch_source_t`是`GCD`内封装的定时器，只依赖于系统内核，所以较准时。



## 1. `NSTimer`用法

### 1.1 创建`NSTimer`

- 通过`scheduledTimer`开头的类方法创建

  ```
  [NSTimer scheduledTimerWithTimeInterval:1.0 repeats:YES block:^(NSTimer * _Nonnull timer) {
       NSLog(@"111");
  }];
  或
  [NSTimer scheduledTimerWithTimeInterval:1.0 target:self selector:@selector(timerAction) userInfo:nil repeats:YES];
  ```

  `timeInterval`执行之前等待的时间。该方法会自动创建`NSTimer`对象，并加入主线程的`RunLoop`的`default mode`上，然后自动启动，执行指定的代码。

- 通过`timerWithTimeInterval`开头的类方法创建

  ```
  NSTimer *timer = [NSTimer timerWithTimeInterval:1.0 repeats:YES block:^(NSTimer * _Nonnull timer) {
       NSLog(@"111");
  }];
  [[NSRunLoop mainRunLoop] addTimer:timer forMode:NSDefaultRunLoopMode];
  或者
  NSTimer *timer = [NSTimer timerWithTimeInterval:1.0 target:self selector:@selector(timerAction) userInfo:nil repeats:YES];
  [[NSRunLoop mainRunLoop] addTimer:timer forMode:NSDefaultRunLoopMode];
  ```

  该方法只创建并返回一个定时器，不会把定时器加入`RunLoop`，需要手动添加到`RunLoop`，然后自动启动，执行指定的代码。



### 1.2 `NSTimer`启动和停止

- 启动

  ```
  [self.timer fire]; // 测试发现没什么用
  self.timer.fireDate = [NSDate distantPast]; // 和self.timer.fireDate = [NSDate distantFuture];对应
  ```

- 停止

  ```
  self.timer.fireDate = [NSDate distantFuture];
  ```

- 设置计时器无效

  ```
  [self.timer invalidate]; 
  ```

  只有设置定时器无效，定时器对当前控制器的持有才会释放，才不会出现循环引用的情况，就算设置定时器为`nil`，也不行；所以通常在`viewWillDisappear`方法中设置定时器无效。

  

  

  

### 1.3 注意事项

`1.1`中创建`NSTimer`的两种方法中，可能会导致`NSTimer`对象和当前控制器对象循环引用：

- 如果是`block`形式执行指定的代码，`NSTimer`对象不会持有当前控制器对象，即使在控制器要销毁的时候，不停止并销毁定时器也不影响控制器的释放，因为`NSTimer`对象没有持有控制器，控制器能正常释放，释放以后定时器还能继续执行指定的代码，因为主`RunLoop`对象持有了定时器，定时器并没有被释放。

- 如果是`target`方式指定执行代码，`NSTimer`会持有当前控制器，如果在控制器释放前不销毁定时器，解除`NSTimer`对象对控制器的持有，那么将会出现循环引用，导致控制器和`NSTimer`对象都不能释放。

- `NSTimer`对其`target`持有的方式是`autorelease`方式, 即`target`会在其指定的`runloop`下一次执行时查看是否进行释放， 若`repeats`参数为`YES`, 则`timer`未释放情况下, `target`不会释放, 因而会引起循环引用; 若`repeats`参数设置为`NO`, 则`target`可以被释放而不会存在循环引用.

- `NSTimer`引发循环引用原理

  > Current RunLoop -> CFRunLoopMode -> sources数组 -> __NSCFTimer -> _NSTimerBlockTarget -> self



### 1.4 `NSTimer`循环引用解决方案

- 引入`WeakContainer`，弱持有`target`对象

  - 新建`WeakContainer`类，内部弱持有`target`，并转发消息

    `WeakContainer.h`

    ```
    #import <Foundation/Foundation.h>
    
    NS_ASSUME_NONNULL_BEGIN
    
    @interface WeakContainer : NSObject
    
    @property (nonatomic, weak) id target;
    
    - (instancetype)initWithTarget:(id)target;
    
    @end
    
    NS_ASSUME_NONNULL_END
    ```

    `WeakContainer.m`

    ```
    #import "WeakContainer.h"
    
    @implementation WeakContainer
    
    - (instancetype)initWithTarget:(id)target
    {
        self = [super init];
        if (self) {
            self.target = target;
        }
        return self;
    }
    
    // 消息转发
    - (id)forwardingTargetForSelector:(SEL)aSelector
    {
        if ([self.target respondsToSelector:aSelector]) {
            return self.target;
        }
        
        return [super forwardingTargetForSelector:aSelector];
    }
    
    
    @end
    ```

  - 设置定时器`target`

    ```
    self.timer = [NSTimer scheduledTimerWithTimeInterval:1.0 target:[[WeakContainer alloc]initWithTarget:self] selector:@selector(timerAction) userInfo:nil repeats:YES];
    ```

  - 在控制器的`dealloc`方法中停止定时器，否则控制器释放，定时器还在运行，导致程序崩溃

    ```
    - (void)dealloc
    {
        self.timer.fireDate = [NSDate distantFuture];
        NSLog(@"释放了%s",__FUNCTION__);
    }
    ```

  

- 使用`NSProxy`抽象类

  > NSProxy是除了NSObject之外的另一个基类，是一个抽象类，只能继承它，重写其消息转发的方法，将消息转发给另一个对象。
  >
  > ```
  > - (void)forwardInvocation:(NSInvocation *)invocation;
  > - (nullable NSMethodSignature *)methodSignatureForSelector:(SEL)sel NS_SWIFT_UNAVAILABLE("NSInvocation and related APIs not available");
  > ```
  >
  > 除了重载消息转发机制的两个方法之外，NSProxy也没有其他功能了。即，使用NSProxy注定是用来转发消息的。
  >
  > - NSProxy可以用来模拟多继承，proxy对象处理多个不同Class对象的消息。
  > - 继承自NSProxy的代理类会自动转发消息，而继承自NSObject的则不会，需要自行根据消息转发机制来进行处理。
  > - NSObject的Category中的方法不能转发

  - 创建`WeakProxy`类

    `WeakProxy.h`

    ```
    #import <Foundation/Foundation.h>
    
    NS_ASSUME_NONNULL_BEGIN
    
    @interface WeakProxy : NSProxy
    
    @property (nonatomic, weak) id target;
    
    - (instancetype)initWithTarget:(id)target;
    
    @end
    
    NS_ASSUME_NONNULL_END
    ```

    `WeakProxy.m`

    ```
    #import "WeakProxy.h"
    
    @implementation WeakProxy
    
    - (instancetype)initWithTarget:(id)target
    {
        self = [WeakProxy alloc];
        self.target = target;
        return self;
    }
    
    - (NSMethodSignature *)methodSignatureForSelector:(SEL)sel {
        return [self.target methodSignatureForSelector:sel];
    }
    
    - (void)forwardInvocation:(NSInvocation *)invocation {
        [invocation invokeWithTarget:self.target];
    }
    
    @end
    ```

    

  - 设置定时器同`WeakContainer`方式



***

## 2. `CADisplayLink`用法

### 2.1 概述

`CADisplayLink`是和屏幕刷新率同步的频率调用`target`的，按照iOS设备屏幕的刷新率60次/秒，就是一秒调用60次`target`。`CADisplayLink`以特定模式注册到`Runloop`后，每当屏幕显示内容刷新结束的时候，`Runloop`就会向`CADisplayLink`指定的`target`发送一次指定的`selector`消息， `CADisplayLink`类对应的`selector`就会被调用一次。

延迟iOS设备的屏幕刷新频率是固定的，CADisplayLink在正常情况下会在每次刷新结束都被调用，精确度相当高。但如果调用的方法比较耗时，超过了屏幕刷新周期，就会导致跳过若干次回调调用机会。如果CPU过于繁忙，无法保证屏幕60次/秒的刷新率，就会导致跳过若干次调用回调方法的机会，跳过次数取决CPU的忙碌程度。



### 2.2 使用场景

从原理上可以看出，CADisplayLink适合做界面的不停重绘，比如视频播放的时候需要不停地获取下一帧用于界面渲染。



### 2.3 基本使用

```
self.displayLink = [CADisplayLink displayLinkWithTarget:self selector:@selector(timerAction)];
[self.displayLink addToRunLoop:[NSRunLoop mainRunLoop] forMode:NSDefaultRunLoopMode];
```

添加到`RunLoop`以后，定时器就开始调用`target`。



### 2.4 主要属性和方法

- `preferredFramesPerSecond属性`：每秒调用`target`的次数，默认和每秒屏幕刷新次数一致，也就是60，设置该属性为1以后，每一秒调用一次`target`
- `paused属性`：判断定时器是否处于暂停状态（getter方法），设置定时器暂停和恢复（setter方法）
- `invalidate方法`：设置定时器无效，和`NSTimer`的该方法效果相同



### 2.5 注意事项

和`NSTimer`一样，都有可能出现循环引用，切记在控制器消失时调用`invalidate`方法。





***

## 3. `dispatch_source_t`用法

### 3.1 基本使用

```
//创建全局队列
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

//使用全局队列创建计时器
_sourceTimer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue);

//定时器延迟时间
NSTimeInterval delayTime = 1.0f;

//定时器间隔时间
NSTimeInterval timeInterval = 1.0f;

//设置开始时间
dispatch_time_t startDelayTime = dispatch_time(DISPATCH_TIME_NOW, (int64_t)(delayTime * NSEC_PER_SEC));

//设置计时器
dispatch_source_set_timer(_sourceTimer,startDelayTime,timeInterval*NSEC_PER_SEC,0.1*NSEC_PER_SEC);

//执行事件
dispatch_source_set_event_handler(_sourceTimer,^{
    NSLog(@"111");
});

//启动计时器
dispatch_resume(_sourceTimer);
```



### 3.2 关键函数

- 设置定时器

  ```
  dispatch_source_set_timer(dispatch_source_t source, 
                            dispatch_time_t start,
                            uint64_t interval, 
                            uint64_t leeway);
  ```

  - `start`：计时器起始时间，可以通过`dispatch_time`创建，如果使用`DISPATCH_TIME_NOW`，则创建后立即执行
  - `interval`：计时器间隔时间，可以通过`timeInterval * NSEC_PER_SEC`来设置，其中`timeInterval`为对应的秒数
  - `leeway`：希望定时器的准确程度。这个参数告诉系统我们需要计时器触发的精准程度。所有的计时器都不会保证100%精准，这个参数用来告诉系统你希望系统保证精准的努力程度。如果你希望一个计时器没五秒触发一次，并且越准越好，那么你传递0为参数。另外，如果是一个周期性任务，比如检查email，那么你会希望每十分钟检查一次，但是不用那么精准。所以你可以传入60，告诉系统60秒的误差是可接受的。这样有什么意义呢？简单来说，就是降低资源消耗。如果系统可以让cpu休息足够长的时间，并在每次醒来的时候执行一个任务集合，而不是不断的醒来睡去以执行任务，那么系统会更高效。如果传入一个比较大的leeway给你的计时器，意味着你允许系统拖延你的计时器来将计时器任务与其他任务联合起来一起执行。

- 暂停定时器

  ```
  dispatch_suspend(dispatch_object_t object);
  ```

- 销毁（停止）计时器

  ```
  //销毁定时器
  dispatch_source_cancel(_sourceTimer);
  ```

- 销毁定时器时调用函数

  ```
  dispatch_source_set_cancel_handler(dispatch_source_t source,
  	dispatch_block_t _Nullable handler);
  ```

  

### 3.3 相比前两个定时器优点

- 时间准确，误差小
- 可以使用子线程，解决定时器跑在主线程上卡UI问题



### 3.4 注意事项

需要将定时器设置为成员变量，且用`strong`修饰，不然会立即释放，如下：

```
@property (nonatomic, strong) dispatch_source_t sourceTimer;
```







