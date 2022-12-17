---
title: "Condition Variable"
date: 2022-12-15T22:05:23+08:00
draft: false
categories:
- 并发编程
tags:
- 条件变量
summary: "为什么需要引入条件变量？为什么条件变量要配合互斥锁使用？条件变量应用于哪些场景？"
---

#### 问题引入
首先考虑这样一个问题，对于一个被若干线程并发访问的共享变量`x`，我们希望实现一个这样的函数：当`x`满足一定条件时，进行一次某种操作（比如，`x>10`时在屏幕上输出`okay!`），借助互斥锁，我们是可以这样实现：
{{< codeblock "Check condition using only lock" "cpp">}}
int x = 0; 
mutex m;
void f() {
    m.lock();
    while(!(x>10)) {
        m.unlock();
        Sleep(100);
        m.lock();
    }
    cout << "okay!" << endl;
    m.unlock();
}
{{< /codeblock >}}
其中最tricky的部分在于，若`Sleep`的时间太短，则程序会在busy waiting中消耗大量的CPU资源；若`Sleep`的时间太长，则会造成函数`f`的响应时间过长。条件变量就致力于解决这一问题。
#### 什么是条件变量
条件变量（condition variable），顾名思义，在逻辑上对应了某种条件（如上面的例子中的`x>2`）。以C++中的`std::condition_variable`为例，它提供了如下三个基本方法：
* `void wait( std::unique_lock<std::mutex>& lock );`原子地release`lock`，将本线程加入`*this`对象的等待队列中，并阻塞，直至被`notify_one`或`notify_all`唤醒后再次require`lock`，并退出；
* `void notify_one() noexcept;` 若有线程在`*this`对象上等待，就唤醒其中的一个；
* `void notify_all() noexcept;`将在`*this`上等待的所有线程唤醒；
使用条件变量解决上面的问题：
{{< codeblock "Check condition using condition variable and lock" "cpp">}}
int x = 0;
mutex m;
condition_variable cond;
void f() {
    unique_lock<mutex> lock(m);
    while(!(x>10)) {
        cond.wait(lock);
    }
    cout << "okay!" << endl;
    lock.unlock();
}
{{</codeblock>}}
并在改变了变量`x`的函数中加入`notify_all`即可。条件变量使得程序在等待条件变化期间无需占用CPU资源，又能在条件变化后及时被唤醒。
实践中，一般条件变量对应的条件较为宽泛（是“`x`的值发生了改变”，而不是`x>2`，因为过细的条件会给`notify`的调用者带来过多的麻烦），所以在`wait`返回后仍需要检查自己的条件是否被满足。而C++同样为这种常见的写法提供了一种`wait`重载:
* `template< class Predicate > void wait( std::unique_lock<std::mutex>& lock, Predicate stop_waiting );`
其行为等价于：
{{< codeblock "" "cpp">}}
while(!stop_waiting()) {
	wait(lock);
}
{{</codeblock>}}
使用此重载，可以进一步简化上面的程序：
{{< codeblock "Simplified version" "cpp">}}
int x = 0;
mutex m;
condition_variable cond;
void f() {
    unique_lock<mutex> lock(m);
    cond.wait(lock, [&]{return x>2;})
    cout << "okay!" << endl;
    lock.unlock();
}
{{</codeblock>}}

#### 另一个视角
假设OS实现了另一种`sleep(chan)`——使当前线程处于sleep状态，直至被chan上的信号唤醒，那么`unlock-sleep-lock`是否就没有问题了呢？答案是否定的：这样的设计会出现所谓lost wakeup问题：试想：当线程A执行了`unlock`但还没执行`sleep(chan)`，此时线程B执行了`wakeup` ，那么这个`wakeup`不会唤醒任何线程，而线程A`sleep`后可能也不会再被唤醒，系统就陷入了停滞状态。所以`cond.wait`中的`unlock`和`sleep`必须是atomic的。
这部分理解来自于6.S081的lecture 13。引用3

#### 为什么条件变量需要和mutex一起使用？
条件变量的行为模式就是在模仿并改进条件等待问题中的互斥锁解决方案，`wait`方法就是对`unlock-Sleep-lock`序列的上位替代，因此需要接收`lock`参数。
从解耦的思想上看，`mutex`解决互斥访问问题，`condition_variable`解决的条件等待问题，一个`mutex`可以对应多个`condition_variable`，将二者分开更加灵活。
#### 条件变量的应用
* [bustub](https://github.com/cmu-db/bustub)使用条件变量实现了[读写锁](https://github.com/cmu-db/bustub/blob/master/src/include/common/rwlatch.h)；
* [bustub](https://github.com/cmu-db/bustub)使用条件变量实现了[`LockManager`中的行锁](https://github.com/cmu-db/bustub/blob/master/src/include/concurrency/lock_manager.h)
#### 参考
1. https://en.wikipedia.org/wiki/Monitor_(synchronization)#Condition_variables
2. https://en.cppreference.com/w/cpp/thread/condition_variable
3. https://pdos.csail.mit.edu/6.S081/2020/lec/l-coordination.txt
