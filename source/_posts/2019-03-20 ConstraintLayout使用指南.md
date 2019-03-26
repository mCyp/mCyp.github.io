---
layout: post
title: "ConstraintLayout使用指南"
date: 2019-03-20 10:23
comments: true
tags: 
	- Android
	- 布局
---

`ConstraintLayout`是2016年Google的I/O大会推出的新型布局-约束布局，话说，今年都2019了，`ConstraintLayout`应该早就被Android工程师熟识了。不过，前几天在群里闲聊的时候，还有人在问：`ConstraintLayout`好用吗？因此，打算写一篇`ConstraintLayout`使用总结。

<!-- more -->

## 目录
![目录](https://upload-images.jianshu.io/upload_images/9271486-f78f1140e91d0fd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 一. 定义
关于这点，我们先看谷歌的官方文档吧：
> A `ConstraintLayout` is a ViewGroup which allows you to position and size widgets in a flexible way.

换成中文就是`ConstraintLayout`可以灵活的设置其他控件的大小和位置。为什么说灵活呢？因为它可以不用写代码，使用鼠标操控就可以直接实现我们的界面，关于直接使用鼠标操作界面的方式，这里我就不再赘述了，请移步郭神的[Android新特性介绍，ConstraintLayout完全解析](https://blog.csdn.net/guolin_blog/article/details/53122387)，本文将侧重用代码讲解`ConstraintLayout`，因为`ConstraintLayout`本身使用好了就可以减少布局的嵌套。
**导入库**
我们在正式介绍之前，先在`build.gradle`文件中添加如下：
```
implementation 'com.android.support.constraint:constraint-layout:1.1.2'
```
## 二. 属性介绍
这里我们先学习一下属性的使用，从基础属性开始吧。如果我直接讲这个属性有什么用可能不是特别清楚，这里我会将这些基础的属性和同类型的`RelativeLayout`属性进行比较：
| ConstraintLayout |              RelativeLayout              | 作用                   |
| :--------------: | :--------------------------------------: | ---------------------- |
|                  |     `layout_constraintLeft_toLeftOf`     | `layout_alignLeft`     |
|                  |    `layout_constraintLeft_toRightOf `    | ` layout_toRightOf`    |
|                  |   ` layout_constraintRight_toRightOf`    | `layout_alignRight`    |
|                  |    `layout_constraintRight_toLeftOf `    | `layout_toLeftOf`      |
|                  |      `layout_constraintTop_toTopOf`      | `layout_alignTop`      |
|                  |    `layout_constraintTop_toBottomOf`     | `layout_below`         |
|                  |  `layout_constraintBottom_toBottomOf `   | `layout_alignBottom`   |
|                  |    `layout_constraintBottom_toTopOf`     | `layout_above`         |
|                  | `layout_constraintBaseline_toBaselineOf` | `layout_alignBaseline` |
对于和left、right相似的start、end基础的属性，这里不再赘述，大家可以自行查阅。当然了，除了一些基础的属性，`ConstraintLayout`也有自己特有的属性，这里向大家介绍一下常用的属性：
### 1. bias（偏移量）
长度和高度的偏移量
|                属性                | 介绍                     |
| :--------------------------------: | ------------------------ |
| `layout_constraintHorizontal_bias` | 水平方向的偏移量（小数） |
|  `layout_constraintVertical_bias`  | 竖直方向的偏移量（小数） |
### 2. Circular positioning（圆形定位）
以一个控件为圆心设置角度和半径定位
|              属性               | 介绍                                                         |
| :-----------------------------: | ------------------------------------------------------------ |
|    `layout_constraintCircle`    | 关联另一个控件，将另一个控件放置在自己圆的半径上，会和下面两个属性一起使用 |
| `layout_constraintCircleRadius` | 圆的半径                                                     |
| `layout_constraintCircleAngle ` | 圆的角度                                                     |
### 3. Percent dimension（百分比布局）
宽高设置百分比长度
|                属性                | 介绍                                                         |
| :--------------------------------: | ------------------------------------------------------------ |
| `layout_constraintWidth_default `  | 宽度类型设置，可以设置`percent`、`spread`和`wrap`            |
| `layout_constraintHeight_default ` | 高度类型设置，同上                                           |
| `layout_constraintWidth_percent `  | 如果`layout_constraintWidth_percent`设置的百分比，这里设置小数，为占父布局宽度的多少 |
| `layout_constraintHeight_percent ` | 设置高度的大小，同上                                         |
### 4. Ratio（比例）
控件的宽和高设置一定比例
|                属性                | 介绍   |
| :--------------------------------: | ------ |
| `layout_constraintDimensionRatio ` | 宽高比 |
### 5. Chain Style（约束链类型）
设置约束链类型，约束链类型包括：`spread`，`spread_inside`和`packed`
|                   属性                    | 介绍       |
| :---------------------------------------: | ---------- |
| `layout_constraintHorizontal_chainStyle ` | 横向约束链 |
|  `layout_constraintVertical_chainStyle `  | 纵向约束链 |
## 三. 具体使用
终于到我们的实战环节了
### 1. 基础属性的使用
先从设置高度和宽度设置开始吧
###### 1.1 宽度和高度的设置
同普通的控件一样，`layout_width`和`layout_height`可以设置如下：
- 具体的值，例如`123dp`
- `wrap_content`，让控件计算出适合自己的大小
- `0dp`，等价与`match_parent`

具体的值和`wrap_content`这里我就不再多介绍了，大家平时用的不少。唯一有区别的是`0dp`这个概念，`LinearLayout`中我们会设置`layout_width`为`0dp`，然后搭配`layout_weight`一起使用，那么我们来看看`ConstraintLayout`如何使用：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/iv_beauty"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        android:scaleType="centerCrop"
        android:src="@drawable/beauty"
        android:layout_width="200dp"
        android:layout_height="0dp" />
</android.support.constraint.ConstraintLayout>
```
我们将高度设置为`0dp`，看看会发生什么？
![高度为0dp](https://upload-images.jianshu.io/upload_images/9271486-d3dbb9d92a4c366f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)与一般布局不一样的是，`ImageView`的高度相当于整个屏幕的高度，等价于`match_parent`。`0dp`完全等价于`match_parent`其实也不一定完全对，我们再来看一下另外一段代码：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- iv_beauty宽100高200，放在左上角 -->
    <ImageView
        android:id="@+id/iv_beauty"
        android:layout_width="100dp"
        android:layout_height="200dp"
        android:scaleType="centerCrop"
        android:src="@drawable/beauty"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <!-- iv_girl宽100高0，放在iv_beauty右边，下面与iv_beauty对齐-->
    <ImageView
        android:id="@+id/iv_girl"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:scaleType="centerCrop"
        android:src="@drawable/girl"
        app:layout_constraintBottom_toBottomOf="@+id/iv_beauty"
        app:layout_constraintLeft_toRightOf="@+id/iv_beauty"
        app:layout_constraintTop_toTopOf="parent" />
</android.support.constraint.ConstraintLayout>
```
效果：
![高度为0dp](https://upload-images.jianshu.io/upload_images/9271486-7eeab285f9800fd1.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)其实可以看出，`0dp`也会受各种约束条件限制，在约束条件内占满整个距离。
###### 1.2 父布局右边
以`ImageView`为例，如果我想将`ImageView`放在父布局的右边，这时我们需要将`layout_constraintRight_toRightOf`的值设置为`parent`，代码如下：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/iv_beauty"
        app:layout_constraintRight_toRightOf="parent"
        android:src="@drawable/beauty"
        android:layout_width="200dp"
        android:layout_height="200dp" />

</android.support.constraint.ConstraintLayout>
```
效果：
![父布局右边](https://upload-images.jianshu.io/upload_images/9271486-5280159f516d1227.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)这个时候你可能会问，如果我想放在父布局左边，是不是只添加`app:layout_constraintLeft_toLeftOf="parent"`就对了？聪明！答对了，同理，`app:layout_constraintTop_toTopOf="parent"`代表放在父布局上边，`app:layout_constraintBottom_toBottomOf="parent"`代表放在父布局下边，这里不再赘述。
###### 1.3 父布局居中
这个时候，小王同学问了：如果我同时将`app:layout_constraintRight_toRightOf="parent"`、`app:layout_constraintLeft_toLeftOf="parent"`、`app:layout_constraintTop_toTopOf="parent"`和`app:layout_constraintBottom_toBottomOf="parent"`都加上会怎么样呢？代码如下：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/iv_beauty"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        android:src="@drawable/beauty"
        android:layout_width="200dp"
        android:layout_height="200dp" />

</android.support.constraint.ConstraintLayout>
```
效果：
![父布局居中](https://upload-images.jianshu.io/upload_images/9271486-3de42b3303493acc.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)我们的`ImageView`居中了，没错，如果添加`app:layout_constraintRight_toRightOf="parent"`和`app:layout_constraintLeft_toLeftOf="parent"`会水平居中，添加`app:layout_constraintLeft_toLeftOf="parent"`和`app:layout_constraintRight_toRightOf="parent"`会竖直居中，全部添加就是水平和竖直方向都居中。
### 2. bias（偏移量）
如果我想`ImageView`相对于父布局左边间隔`1/10`个父布局长度，相对于父布局上边间隔`2/10`个父布局高度，我们该怎实现呢？看代码：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/iv_beauty"
        app:layout_constraintHorizontal_bias="0.1"
        app:layout_constraintVertical_bias="0.2"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        android:src="@drawable/beauty"
        android:layout_width="200dp"
        android:layout_height="200dp" />

</android.support.constraint.ConstraintLayout>
```
效果：
![bias](https://upload-images.jianshu.io/upload_images/9271486-55952a9c96e2e64b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
`app:layout_constraintHorizontal_bias="0.1"`代表水平方向往右偏移10%，`app:layout_constraintVertical_bias="0.2"`代表竖直方向往下偏移20%。
这里需要注意的是：
- 想要设置偏移，必须先将控件设置父布局居中。
- 偏移的长度，如横向的偏移，是父布局宽度减去`ImageView`宽度的剩下的10%
### 3. Percent dimension
如果我们想将`ImageView`的宽度设置为父布局宽度的70%，这个时候我们就可以考虑使用百分比属性了，代码如下：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/iv_beauty"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintWidth_default="percent"
        app:layout_constraintWidth_percent="0.2"
        android:scaleType="centerCrop"
        android:src="@drawable/beauty"
        android:layout_width="0dp"
        android:layout_height="200dp" />

</android.support.constraint.ConstraintLayout>
```
效果：
![百分比](https://upload-images.jianshu.io/upload_images/9271486-d34c0dc2add176ca.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
可以看到，我们这里先将`layout_constraintWidth_default`属性设置为`percent`，接着将`layout_constraintWidth_percent`设置为`0.2`，同理，我们可以对`layout_constraintHeight_default`和`layout_constraintHeight_percent`进行高度的设置。这里需要注意的是：
- 需要先将布局约束之后，才可以进行宽度或者高度的设置，如上述代码，我们先添加了`app:layout_constraintTop_toTopOf="parent"`和`app:layout_constraintLeft_toLeftOf="parent"`，将`ImageView`设置在左上角，如果没有先进行控件的约束，会发现我们的设置的百分比这个属性没有起作用。后面的很多属性也是如此，需要先对控件进行约束，才能对我们的控件设置一些属性。
### 4. Ratio
在日常工作中，我们会经常遇到将控件比如`ImageView`设置长:宽=2:1，这个时候怎么办呢？这个时候，小王说了，这个简单啊，如果宽设置为`200dp`，高就设置为`100dp`，我只能说，没毛病。同样的功能我们也可以通过`Ratio`实现，看代码：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/iv_beauty"
        android:layout_width="200dp"
        android:layout_height="0dp"
        android:scaleType="centerCrop"
        app:layout_constraintDimensionRatio="2:1"
        android:src="@drawable/girl"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</android.support.constraint.ConstraintLayout>
```
效果：
![ratio](https://upload-images.jianshu.io/upload_images/9271486-b46c0cf587119fed.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如果需要按照比例设置控件，这里需要先将`layout_width`或者`layout_height`一方设置为`0dp`，另一个设置为正常比例，接着给`layout_constraintDimensionRatio`设置比例，例如`app:layout_constraintDimensionRatio="2:1"`.
### 5. chain
Chain(约束链)是`ConstraintLayout`中比较有趣的功能了，我们这次先不看代码，看一下关于约束链的图片，图片来自[Medium](https://medium.com/androiddevelopers/introducing-constraint-layout-1-1-d07fc02406bc)：
![约束链的三种类型](https://upload-images.jianshu.io/upload_images/9271486-42765eab507b1a6b.gif?imageMogr2/auto-orient/strip)这种动图很好的向我们诠释了约束链的三种类型，分别是`spread`、`spread_inside`和`packed`：
- `spread`：视图均匀分布。
- `spread_inside`：除了约束链的头部和尾部贴在两边的约束上，其余均匀分布。
- `packed`：将视图打包在一起，默认居中。

**第一步 建立约束链**
什么叫约束链呢，看图：
![约束链](https://upload-images.jianshu.io/upload_images/9271486-36da0244b46d715c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)形成约束链的条件就是如图两个`ImageView`分别以对方为约束，我们等会儿看代码。
**第二步 在第一个控件添加约束链类型**
`layout_constraintVertical_chainStyle`或者`layout_constraintHorizontal_chainStyle`添加上面介绍的约束链类型。
以`spread`为例，我们看一下代码：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- 在第一个控件设置chain_style，第一个和最后一个ImageView也要设置
    好约束，上下控件也要互为约束 -->
    <ImageView
        android:id="@+id/iv_beauty"
        android:layout_width="200dp"
        android:layout_height="100dp"
        android:scaleType="centerCrop"
        android:src="@drawable/beauty"
        app:layout_constraintBottom_toTopOf="@+id/iv_girl"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_chainStyle="spread" />

    <ImageView
        android:id="@+id/iv_girl"
        android:layout_width="200dp"
        android:layout_height="100dp"
        android:scaleType="centerCrop"
        android:src="@drawable/girl"
        app:layout_constraintBottom_toTopOf="@+id/iv_biu"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/iv_beauty" />

    <ImageView
        android:id="@+id/iv_biu"
        android:layout_width="200dp"
        android:layout_height="100dp"
        android:scaleType="centerCrop"
        android:src="@drawable/biu"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/iv_girl" />


</android.support.constraint.ConstraintLayout>
```
效果：
![spread效果](https://upload-images.jianshu.io/upload_images/9271486-17496b9b6dc52a49.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
`spread`类型的约束链妥妥的完成了，可以根据自己的需求修改约束链类型。
### 6. GuideLine
`GuideLine`只能用在`ConstraintLayout`布局中，用来辅助布局，不会被显示，通常有横向和纵向之分。`GuideLine`更多的是使用鼠标操控实现布局，不过，我们就简单的讲解一下使用代码的方式，我们的目标是实现两侧分布的效果，看代码（基于上面的`spread`效果的代码）：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/iv_beauty"
        android:layout_width="200dp"
        android:layout_height="100dp"
        android:layout_marginEnd="8dp"
        android:scaleType="centerCrop"
        android:src="@drawable/beauty"
        app:layout_constraintBottom_toTopOf="@+id/iv_girl"
        app:layout_constraintEnd_toStartOf="@+id/guideline"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintVertical_chainStyle="spread" />

    <ImageView
        android:id="@+id/iv_girl"
        android:layout_width="200dp"
        android:layout_height="100dp"
        android:layout_marginStart="8dp"
        android:scaleType="centerCrop"
        android:src="@drawable/girl"
        app:layout_constraintBottom_toTopOf="@+id/iv_biu"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintStart_toStartOf="@+id/guideline"
        app:layout_constraintTop_toBottomOf="@+id/iv_beauty" />

    <ImageView
        android:id="@+id/iv_biu"
        android:layout_width="200dp"
        android:layout_height="100dp"
        android:layout_marginEnd="8dp"
        android:scaleType="centerCrop"
        android:src="@drawable/biu"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@+id/guideline"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/iv_girl" />

    <!-- Guideline可以设置具体的宽度，也可以设置百分比 -->
    <android.support.constraint.Guideline
        android:id="@+id/guideline"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_percent="0.5" />
</android.support.constraint.ConstraintLayout>
```
效果：
![Guideline效果](https://upload-images.jianshu.io/upload_images/9271486-c57b1128a4b545a5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)我们这里用的Android studio编辑模式查看的，真实的情况下`约束链`和`Guideline`是不会显示的，当`Guideline`移动的时候以`Guideline`为约束的控件也会跟着移动。
### 7. Barrier
`Barrier`同`Guideline`一样，不会被显示，你可以理解为`Barrier`为我们组成了一个虚拟的视图组，不过它没有视图层级的概念。我们看一下`Barrier`的使用效果：
![Barrier效果](https://upload-images.jianshu.io/upload_images/9271486-fe21f14dc464d48f.gif?imageMogr2/auto-orient/strip)可以看到，中间的线就是我们的`Barrier`，会随着左侧控件组的最长侧变化而改变`Barrier`的位置，从而改变右侧控件的位置。同样推荐使用鼠标操控的方式使用`Barrier`实现布局，这里只是简单的使用：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="100dp"
    android:background="@drawable/shape_bg">

    <ImageView
        android:id="@+id/iv_biu"
        android:layout_width="80dp"
        android:layout_height="80dp"
        android:scaleType="centerCrop"
        android:src="@drawable/biu"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toRightOf="@+id/barrier"
        android:layout_marginStart="10dp"
        app:layout_constraintTop_toTopOf="parent" />

    <!-- constraint_referenced_ids包含我们需要包含的组件，以这些组件的某一侧为基准 -->
    <!-- barrierDirection面向包含的组件 -->
    <android.support.constraint.Barrier
        android:id="@+id/barrier"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        app:barrierDirection="right"
        app:constraint_referenced_ids="tv_name,tv_content" />

    <TextView
        android:id="@+id/tv_name"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintTop_toTopOf="parent"
        android:layout_marginTop="20dp"
        android:layout_marginStart="20dp"
        app:layout_constraintLeft_toLeftOf="parent"
        android:text="Li Lei"
        android:textSize="20sp" />

    <TextView
        android:id="@+id/tv_content"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="20dp"
        android:layout_marginStart="20dp"
        app:layout_constraintLeft_toLeftOf="parent"
        android:text="look for bigger world,looking for"
        android:textSize="16sp"
        app:layout_constraintBottom_toBottomOf="parent" />
</android.support.constraint.ConstraintLayout>
```
效果：
![Barrier效果](https://upload-images.jianshu.io/upload_images/9271486-2c0b012117a3ce7f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
什么情况下会使用`Barrier`呢？当你使用不可控长度的文本或者自定义的控件的时候，可以考虑使用`Barrier`。
### 8. Circular positioning
什么是`Circular positioning`呢？我们可以称之为圆形定位，就是以目标控件为圆心，通过设置角度和半径确定我们当前控件的位置，如官方图：
![Circular positioning](https://upload-images.jianshu.io/upload_images/9271486-5e66d03342f5ce55.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)可以说是简单明了了，那么我们来学习一下简单使用：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/iv_biu"
        android:layout_width="64dp"
        android:layout_height="64dp"
        android:scaleType="centerCrop"
        android:src="@drawable/biu"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        android:layout_marginStart="10dp"
        app:layout_constraintTop_toTopOf="parent" />

    <ImageView
        android:id="@+id/iv_girl"
        android:layout_width="64dp"
        android:layout_height="64dp"
        app:layout_constraintCircle="@+id/iv_biu"
        app:layout_constraintCircleRadius="120dp"
        app:layout_constraintCircleAngle="35"
        android:src="@drawable/girl"/>

</android.support.constraint.ConstraintLayout>
```
效果：
![Circular Positioning](https://upload-images.jianshu.io/upload_images/9271486-b0d73416b13700f8.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么`Circular positioning`可以做什么呢？可以用来设计比较炫酷的动画或者控件：
![行星动画](https://upload-images.jianshu.io/upload_images/9271486-9b04c9d60b693c88.gif?imageMogr2/auto-orient/strip)
地址：[ConstraintLayout: Circular Positioning](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2017/1015/8597.html)
如果想查看关于`Circular positioning`的进阶使用，可以查看我的另一篇博客：
> [妙用ConstraintLayout的Circular positioning](https://www.jianshu.com/p/7f111f0bdbd0)

### 9. Group
`Group`的作用感觉比较鸡肋，通过`app:constraint_referenced_ids`添加对子View的引用，然后统一的显示或者隐藏控件。这里不再赘述。
## 四. 总结
`ConstraintLayout`介绍完毕，需要多多使用才能很好的掌握。本人水平有限，难免有误，如有错误，欢迎提出。
引用：
[Introducing Constraint Layout 1.1](https://medium.com/androiddevelopers/introducing-constraint-layout-1-1-d07fc02406bc)
[ConstraintLayout: Circular Positioning
](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2017/1015/8597.html)[官方文档](https://developer.android.com/reference/android/support/constraint/ConstraintLayout)










