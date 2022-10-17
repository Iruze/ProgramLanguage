# python中的多线程，多进程，协程

- [Python多进程、多线程和协程简介](https://www.cnblogs.com/dogecheng/p/11439912.html)

- [Python进程、线程、协程详解](https://www.cnblogs.com/zhangliang91/p/10547551.html)

- [python详解threading模块:Condition类的使用](https://blog.csdn.net/brucewong0516/article/details/84587522)

- [leetcode多线程多进程题解](https://github.com/Iruze/SolutionsOnLeetcodeForZZW/blob/master/SolutionsSummary/%E5%A4%9A%E7%BA%BF%E7%A8%8B%26%E5%A4%9A%E8%BF%9B%E7%A8%8B.md)

- [Python eventlet](https://cloud.tencent.com/developer/article/1406362)

# python中的协程
#### 什么是协程
<details>
  <summary>展开</summary>
  
  协程是一种用户态的轻量级线程，协程的调度完全由用户控制。协程拥有自己的寄存器上下文和栈。协程调度切换时，将寄存器上下文和栈保存到其他地方，在切回来的时候，恢复先前保存的寄存器上下文和栈，直接操作栈则基本没有内核切换的开销，可以不加锁的访问全局变量，所以上下文的切换非常快。
</details>

#### 协成与线程比较
<details>
  <summary>展开</summary>
1). 一个线程可以拥有多个协程，一个进程也可以单独拥有多个协程，这样python中则能使用多核CPU。

2). 线程进程都是同步机制，而协程则是异步

3). 协程能保留上一次调用时的状态，每次过程重入时，就相当于进入上一次调用的状态

</details>

#### python协成实现 生产者－消费者

<details>
    <summary>首先复习下生成器</summary>
  
参考：　https://www.cnblogs.com/fcyworld/p/6275563.html

协程的实现为协作式而非抢占式的，这是和进程线程的最大区别。在Python中，利用`yield`和`send`可以很容易实现协程。

  
如果一个函数使用了 `yield` 语句，那么它就是一个生成器函数。当调用这个函数时，它返回一个迭代器。当第一次调用 `__next__()` 时候，生成器函数主体开始执行，遇到 `yield` 表达式时候终止。

当使用`__next__()`方法时候，`yield value`语句返回`None`；当使用`send(v)`方法时候，`yield value`返回`v`。也就是说，`__next__()`方法相当于`send(None)`方法

```python3
def consumer():
    while True:
        line = yield                            #line接收的是yield这个表达式的返回值！
        print(line.upper())


def productor():
    with open('text.txt') as file:
        for i, line in enumerate(file):
            yield line
            print("{0} lines".format(i))


c = consumer()
c.__next__()                                   #手动启动生成器，注意在Python3.X中不是c.next()
for i in productor():
    c.send(i)
```
</details>

<details>
    <summary>廖雪峰[生产者-消费者]写法</summary>
  
参考： https://www.liaoxuefeng.com/wiki/1016959663602400/1017968846697824 
 
```python
def consumer():
    r = ''
    while True:
        n = yield r
        if not n:
            return
        print('[CONSUMER] Consuming %s...' % n)
        r = '200 OK'

def produce(c):
    c.send(None)
    n = 0
    while n < 5:
        n = n + 1
        print('[PRODUCER] Producing %s...' % n)
        r = c.send(n)
        print('[PRODUCER] Consumer return: %s' % r)
    c.close()

c = consumer()
produce(c)
```
执行结果:     
```shell
[PRODUCER] Producing 1...
[CONSUMER] Consuming 1...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 2...
[CONSUMER] Consuming 2...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 3...
[CONSUMER] Consuming 3...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 4...
[CONSUMER] Consuming 4...
[PRODUCER] Consumer return: 200 OK
[PRODUCER] Producing 5...
[CONSUMER] Consuming 5...
[PRODUCER] Consumer return: 200 OK               
```
- 参考： [python中yield的用法详解——最简单，最清晰的解释](https://blog.csdn.net/mieleizhi0522/article/details/82142856)
                
                
</details>

#### python用装饰器实现一个单例模式

<details>
    <summary>实现</summary>
    
```python
#!/usr/bin/python

# -*- coding: utf-8 -*-
import time
import functools


# 使用装饰器实现单例模式
def singleton(cls):
    instance = {}

    @functools.wraps(cls)
    def _inst(*args, **kwargs):
        if cls not in instance:
            instance[cls] = cls(*args, **kwargs)
        return instance[cls]
    return _inst


@singleton
class A:
    pass
``` 
</details>
