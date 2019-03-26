---
layout: post
title: "Java基础-浅谈关键字volatile和synchronized关键字"
date: 2019-03-06 10:23
comments: true
tags: 
	- Java
	- 并发
---

## 前言 

这几天一直在学习Java并发相关的知识（比刚上大学那会儿看好多了，哈~），所以觉得有必要做一些知识总结。当我们在谈到并发或者查看一些开源库的源码的时候，我们会经常看到关键字`volatile`和 `synchronized`，如果不了解这些关键字，其实还是挺头疼的。

<!-- more -->

## 目录

![目录](https://upload-images.jianshu.io/upload_images/9271486-a17ec4587f06855e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 一. 定义

`volatile`和`synchronized`是Java中的关键字

## 二. 作用

我们都知道，Java中某个线程在第一次读取共享属性之前，会从主内存中复制这个属性到线程的工作内存中，之后才会操作这个共享属性，在操作完成之后，如果共享属性的值发生了修改，则会先保存到本地的工作线程，然后才会刷新到主内存（貌似跟作用没什么关系，不过需要理解）。此外，在正式介绍这两个关键字作用之前，我觉得有必要了解三个名词：原子性、可视性和有序性
|  名词  | 解释                                                         |
| :----: | ------------------------------------------------------------ |
| 原子性 | `一个操作/一系列操作`要么全部执行要么都不执行                |
| 可视性 | 如果一个线程对一个共享值作出了一些修改，其他线程都可以看到这个共享值的修改。 |
| 有序性 | 程序的运行顺序要和程序的逻辑顺序一致，可能实际情况是计算机考虑到性能的因素执行顺序有所不同，但是结果肯定会跟逻辑顺序一致。 |
下面来介绍我们今天的主角：
**1. volatile**
对于volatile的作用，《Java核心技术卷》是这么说的：

>为`实例域的访问`提供了一种`免锁机制`，不提供`原子性`。

首先，`实例域`说明它是用来修饰域的，如下：
```
private volatile boolean isNum = false;
```
其次，`对实例域的访问提供了免锁机制`说明`volatile`具有`可视性`；最后的重点就是没有原子性。《Java编程思想》中提到：
>所有的基础类型（除了`double`和`long`）的基础操作（读取和写入）都具有原子性，经过`volatile`修饰的`long`和`double`在基础操作中也会具有原子性。

所以`volatile`除了提供可视性之外，还能够为`long`和`double`的读取和写入提供原子性，此外，`volatile`还可以保证有序性。
**2. synchronized**
在Java中，所有实例对象都自动含有单一的锁（也称监视器）。所以，当使用`synchronized`修饰的方法的时候，该方法会自动给实例加锁，这个时候，其他含`synchronized`的方法必须等到该方法调用结束并释放锁之后，该方法才能够被调用。简单来说，就是同一时间内，只能有一个线程访问 `synchronized`修饰的方法或者代码块啊，保证了原子性、有序性和可视性。

## 三. 使用

**1. volatile**
`volatile`的使用比较简单：

```
// 对一个域加上volatile，域可以确保可视性和有序性
private volatile int b;

// 对long和double加上volatile，可以使如下的读取和写入具有原子性。
private volatile long a;

public long getA(){ return a;}
public void setA(A a){ this.a = a;}
```
**2. synchronized**
相对于`volatile`的使用，`synchronzied`的使用相对复杂一点：
```
public class Test {
	private int a;
	public static int b；

	public synchronized void addOne() {
		a = a + 1;
	}

	public void addTwo() {
		synchronized (this) {
			a = a + 2;
		}
	}

	public void addThree() {
		synchronized (Test.class) {
			b = b + 3;
		}
	}

	public synchronized static void addFour() {
		b = b + 4;
	}
}
```
根据以上代码，我们看到`synchronized`的使用通常会分两种类型：
- 对象锁，`synchronized`修饰普通方法或者在代码块中使用的当前对象，分别对应着`addOne()`和`addTwo()`方法，该方法采用的是`Test`的对象锁，只针对单个Test实例的并发问题。
- 类锁，`synchronized`修饰静态方法或者在代码块中使用类对象，分别对应这`addFour()`和`addThree()`，这两个方法采用的是`Test.Class`的对象锁。当存在多个Test实例对象的时候，同一时间只有一个Test实例可以访问到`addFour()`方法或者`addThree()`中的代码块。

值得注意的是，一个任务可以多次获得对象的锁，当在一个`synchronized`方法中调用同一个对象的另一个`synchronized`方法，该方法就会使得JYM跟踪的线程的持有的锁的数量加一，方法调用结束的时候，持有锁的数量就会减一，直到数字减为零，锁才会被释放。
## 四. 进一步探究

在这里，我准备向各位同学浅析一下`volatile`和`synchronized`的作用。
**1.volatile**
从第二部分中，我们了解到`volatile`能够保证有序性和可视性以及为`double`和`long`的读取和写入操作的原子性，除此之外，不会保证其他操作的原子性。

- 有序性：（1）当执行包含volatile的域的读操作或者写操作的时候，在其前面的操作肯定已经全部进行，且结果对后面的操作可见，在其后面的操作肯定还没有进行。（2）Java在进行指令优化的时候，不能把`volatile域的操作语句`的前面的语句放在后面执行，也不能将其后面的语句放在前面执行。
- 可视性：关于可视性我们直接引用《Java编程思想》的原文
> 只要对`volatile`域产生了写操作，那么所有的读操作都可以看到这个修改。即便使用了本地缓存，情况也确实如此，`volatile`域会被立即写入主存中，而读取操作就发生在主存中。

- 非原子性：在Java中，只有对基本类型（除了`long`和`double`，但是`long`和    `double`经`volatile`修饰后读取和写入会具有原子性）的变量的
  读取和写入才会确保有原子性，并且不包括自增这类跟之前值相关的操作，原因如下：
```
volatile int num = 1;
public void doAddOne() {
  num++;
  // 自增包括两个步骤：
  // 1. 读取num的值 2. num+1 写入修改后的值
  // 假设线程A在读取num值的时候，被挂起，这个时候线程B读取num的值
  // 线程B对num进行自增，结果为1
  // 这个时候，线程A恢复调度，对起初读取的num的值进行自增，结果也会1
  // 0经过两个自增，结果仍然为1，所以说volatile域并不能保证原子性
}
```
**2. synchronized**
这里，我们就简单的讨论一下`synchronized`的执行过程吧：
（1）获得同步锁。
（2）清空工作内存。
（3）从主内存中复制数据副本到工作内存。
（4）执行代码。
（5）刷新数据到主内存。
（6）释放锁。
这也就是`synchronized`能够具有可视性、有序性和原子性的原因。
## 五. 总结

![总结](https://upload-images.jianshu.io/upload_images/9271486-5c1ebf954f37c1be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 六. 拓展

其他解决并发的方式：

> [Java基础-浅析解决并发的几种方式](https://www.jianshu.com/p/ce1dc6cfe919)

## 七. 引用

本人水平有限，难免会有错误，如有错误，欢迎各位同学指出～
如下是本人的参考：

1. 书籍：《Java核心技术卷》《Java编程思想》
2. 博客：
    [Java：手把手教你全面学习神秘的Synchronized关键字](https://www.jianshu.com/p/2ed498b43628)
    [Java：鲜为人知的关键字volatile](https://www.jianshu.com/p/31e5ab16935f)
    [ java本地内存什么时候刷新到主内存中去](https://segmentfault.com/q/1010000015787014)










