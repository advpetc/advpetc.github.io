---
layout:     post
title:      "多线程简介与应用"
subtitle:   "\"关于性能提升 \""
date:       2017-06-27 2:39
author:     "Haoyang"
header-img: "img/book-bg.jpg"
catalog:    true
tags:
    -Python
---

>以下内容基于Python 3

## Threading简介

在Python中有一个很有争议的包，叫做threading/thread，翻译成中文就是线程的意思。用比较形象的例子来解释什么是线程，我想便是小时候老师让我们学习的时候不要三心二意，不然会降低自己的学习效率。然而在计算机中，同时做多个事情反而会提升我们程序的运行效率，但是也同时会占据更多的内核陈本来达到其目的，所以有很多多核的电脑会很快。

说Python的threading鸡肋的原因是因为其GIL锁的历史遗留问题。GIL会在开启多线程达到一定占用度的时候自动保护CPU的安全来组织使用，说白了就是一个锁，也就是说并不可以完全使用所有的内核。但是像C++就没没有这种锁，可以完全的使用（比如在写一个死循环的时候是可以把ram完全用尽的），但是却没有多线程😅。

在使用网络爬虫，搜索engine的时候多线程都会比较有用，在前端中可以制作一个类似loading bar的进度条来随时跟踪状态，可以比较好的提高用户体验。

## 模块介绍

### Thread
在一般使用中都会将具体的数据处理过程包装成一个独有的threading类中，然后继承threading.Thread的super class的各种函数。当然也可以单独使用，例如：
```python
thread.start_new_thread(function, args[,kwargs])
```
其中function是使用的包装的函数（用来做数据处理），后面的args是输入的参数。简单的例子：
```python
import thread

def data_process(my_thread, data)
    print(my_thread, data)

try:
    thread.start_new_thread(data_process, ('First thread', 1,))
    thread.start_new_thread(data_process, ('Second thread', 2,))
except:
    print('Cannot threading')
```

输出结果为：

```
First thread 1
Second thread 2
```
同样也可以用for loop循环生成thread。


### Threading
与thread相对应的便是基于类的threading，也是目前使用最广泛的多线程方式。我们自己写的threading继承对象便是threading.Thread，首先介绍threading类的特征函数：
首先是constructor:
```python
def __init__(self, group=None, target=None, name=None,
                 args=(), kwargs=None, *, daemon=None):
```
* group默认为None，在之后的Python版本中会支持ThreadGroup

* target默认为None，在运行之后的run()方法的时候的对象，一般为其自己本身

* name默认为None，为每一个线程赋名，如果为None则依照"Thread-N"的规则，其中N为decimal

* args默认为空，目的为实现对像赋参数，之后的例子会详细解释

* kwargs默认为None/{}，为可选参数，注意这里是字典/dictionary类型

* daemon默认为None，为最终threading退出类型，在整个进程结束之后也意味着没有daemon线程再运行，在官方解释上是这样的：

>A thread can be flagged as a “daemon thread”. The significance of this flag is that the entire Python program exits when only daemon threads are left. The initial value is inherited from the creating thread. The flag can be set through the daemon property or the daemon constructor argument.

同样threading也有很多build-in functions/methods:
1. **start()**：开启运行threading，注意每个thread都只能start一次，如果多次python会报RunTimeError
2. **run()**：具体threading的工作流程，可以理解成要做的事情，在使用的时候一般会改写。
3. **join(timeout=None)**：timeout默认为None，这个参数在documentations上没有明确给出说明，只说了当不为None的时候当作0来考虑等待时间进行上锁；这个函数的目的是终止／暂停正在运行的线程，只有当关闭时线程才可以恢复。
5. **getName()**, **setName()**，分别为getter和setter，在此不做赘述。

下面给一个小例子帮助理解：
```python
import threading, requests, Queue, json


class Producer(threading.Thread):
    def __init__(self, engine, data):
        threading.Thread.__init__(self)
        self.engine = engine
        self.data = data

    def run(self):
        send = {'type': self.engine}
        host = "your_host_urls"
        headers = {
            'content-type': 'your_content_type',
            'accept': 'your_accept_datatype'
        }
        # assuming this is post requests
        r = requests.post(host, json=send, headers=headers)
        self.data.put(json.loads(r.content.decode('utf-8')))

```

调用如下：

```python
out_data = queue.Queue()

for engine in engines:

    thread = Producer(engine, out_data)
    thread.start()
    threads.append(thread)
for thread in threads:
    thread.join()
```

* 使用方法比较简单，在run的时候调用了第三方api，在使用Multithreading中是经常用的功能，另外需要注明的是在python中只有Queue可以保证与线程同步输出结果：在线程使用的时候并非按照顺序一个一个进行，因此如果想要得到相应的数据要考虑同步问题。
* 另外一种强制线程同步的方法是添加锁的概念，以下实例来源于[网络](http://www.runoob.com/python/python-multithreading.html):

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-

import threading
import time

class myThread (threading.Thread):
    def __init__(self, threadID, name, counter):
        threading.Thread.__init__(self)
        self.threadID = threadID
        self.name = name
        self.counter = counter
    def run(self):
        print "Starting " + self.name
       # 获得锁，成功获得锁定后返回True
       # 可选的timeout参数不填时将一直阻塞直到获得锁定
       # 否则超时后将返回False
        threadLock.acquire()
        print_time(self.name, self.counter, 3)
        # 释放锁
        threadLock.release()

def print_time(threadName, delay, counter):
    while counter:
        time.sleep(delay)
        print "%s: %s" % (threadName, time.ctime(time.time()))
        counter -= 1

threadLock = threading.Lock()
threads = []

# 创建新线程
thread1 = myThread(1, "Thread-1", 1)
thread2 = myThread(2, "Thread-2", 2)

# 开启新线程
thread1.start()
thread2.start()

# 添加线程到线程列表
threads.append(thread1)
threads.append(thread2)

# 等待所有线程完成
for t in threads:
    t.join()
print "Exiting Main Thread"
```

* 注意在其中使用的 **threadLock.acquire()** 与 **threadLock.release()** 的为止--在需要处理的数据之前与之后，同时在外使用是不要忘记initialize threadLock，使用 **threading.Lock()**

## 后记

1. 在使用多线程的时候要先抽象化自己想干的具体事情，不要盲目的当作一个比较快的forloop来看待，盲目开启过多线程数反而适得其反，要在适当的数量下完全发挥其功用，否则很多情况下反而并非像想象中那么快。
2. 以上只是两个比较一般的例子，在实际中会用多线程来处理数据，在用户上传文件中可以充当parse，analyze和write的功用，当然这些甚至可以和之前的调用api相互契合使用。

--Haoyang
