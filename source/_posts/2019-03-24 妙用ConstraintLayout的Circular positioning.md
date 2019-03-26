---
layout: post
title: "妙用ConstraintLayout的Circular positioning"
date: 2019-03-20 10:23
comments: true
tags: 
	- Android
	- 布局
---

在上一篇中，我们学习了`ConstraintLayout`的基本使用，如果还不了解`ConstraintLayout`，快去学习一下吧：
> [ConstraintLayout使用指南](https://www.jianshu.com/p/958887ed4f5f)

我们这一篇主要讲解一下`ConstraintLayout`的`Circular positioning`功能。

<!-- more -->

## 目录
![目录](https://upload-images.jianshu.io/upload_images/9271486-526a3bcf1b55890d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 一. 介绍
什么是`Circular positioning`呢？我们可以称之为圆形定位，就是以目标控件为圆心，通过设置角度和半径确定我们当前控件的位置，如官方图：
![Circular positioning](https://upload-images.jianshu.io/upload_images/9271486-5e66d03342f5ce55.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## 二. Circular positioning使用
关于入门使用，可以查看我的前一篇博客关于`Circular positioning`的使用，主要是`layout_constraintCircle`锁定目标控件，`layout_constraintCircleRadius`和`layout_constraintCircleAngle`分别控制半径和角度。这里不再赘述
> [ConstraintLayout使用指南](https://www.jianshu.com/p/958887ed4f5f)
## 三. Circular positioning进阶
我们先来看一下目标效果：
![最终效果](https://upload-images.jianshu.io/upload_images/9271486-84bfce456df76616.gif?imageMogr2/auto-orient/strip)
#### 1. 设置布局
布局的xml文件比较长，内容其实很简单，主要是四个`FloatingActionButton`和三个`Group`，这个时候你可能会有疑惑，为什么会有三个`Group`？我这里解答一下，上篇我们讲了，`Group`在的`ConstraintLayout`中用来统一的控制视图的显示和隐藏，如果只用一个`Group`并不能让我们的控件有序的显示和隐藏，而`FloatingActionButton`由于不能使用`setVisibility`方法，只能使用`Group`管理`FloatingActionButton`的显示和隐藏，因此使用三个`Group`来实现上图三个`FloatingActionButton`有序的显示和隐藏（本来打算使用`FloatingActionButton`代替`ImageView`减少工作量的，`FloatingActionButton`导致的问题反而使工作量增加了，哈哈～）,`activity_constraint.xml`如下：
```
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.orient.test.ui.activity.ConstraintActivity">

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab_add"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="32dp"
        android:layout_marginEnd="32dp"
        android:backgroundTint="@color/colorAccent"
        android:padding="10dp"
        android:src="@drawable/ic_constraint_add"
        app:fabSize="normal"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:pressedTranslationZ="20dp"
        app:rippleColor="#1f000000" />

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab_like"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="32dp"
        android:layout_marginEnd="32dp"
        android:visibility="gone"
        android:backgroundTint="@color/colorAccent"
        android:padding="10dp"
        android:src="@drawable/ic_constraint_like"
        app:fabSize="normal"
        app:layout_constraintCircle="@+id/fab_add"
        app:layout_constraintCircleRadius="80dp"
        app:layout_constraintCircleAngle="270"
        app:pressedTranslationZ="20dp"
        app:rippleColor="#1f000000" />

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab_write"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="32dp"
        android:layout_marginEnd="32dp"
        android:backgroundTint="@color/colorAccent"
        android:padding="10dp"
        android:src="@drawable/ic_constraint_write"
        app:fabSize="normal"
        app:layout_constraintCircle="@+id/fab_add"
        app:layout_constraintCircleRadius="80dp"
        app:layout_constraintCircleAngle="315"
        app:pressedTranslationZ="20dp"
        app:rippleColor="#1f000000" />

    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab_top"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="32dp"
        android:layout_marginEnd="32dp"
        android:backgroundTint="@color/colorAccent"
        android:padding="10dp"
        android:src="@drawable/ic_constraint_top"
        app:fabSize="normal"
        app:layout_constraintCircle="@+id/fab_add"
        app:layout_constraintCircleRadius="80dp"
        app:layout_constraintCircleAngle="360"
        app:pressedTranslationZ="20dp"
        app:rippleColor="#1f000000" />

    <android.support.constraint.Group
        android:id="@+id/gp_like"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:constraint_referenced_ids="fab_like"/>

    <android.support.constraint.Group
        android:id="@+id/gp_write"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:constraint_referenced_ids="fab_write"/>

    <android.support.constraint.Group
        android:id="@+id/gp_top"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:constraint_referenced_ids="fab_top"/>

</android.support.constraint.ConstraintLayout>
```
#### 2. 编写代码
首先确定我们需要使用的实例：
```
    private FloatingActionButton mAdd;
    private FloatingActionButton mLike;
    private FloatingActionButton mWrite;
    private FloatingActionButton mTop;
    private Group likeGroup;
    private Group writeGroup;
    private Group topGroup;
    // 动画集合，用来控制动画的有序播放
    private AnimatorSet animatorSet;
    // 圆的半径
    private int radius;
    // FloatingActionButton宽度和高度，宽高一样
    private int width;
```
接着初始化我们的控件，这里的代码比较简单，`initListener()`我们放在后面介绍：
```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_constraint);

        initWidget();
        initListener();
    }

    @Override
    protected void onResume() {
        super.onResume();

        // 动态获取FloatingActionButton的宽
        mAdd.post(new Runnable() {
            @Override
            public void run() {
                width = mAdd.getMeasuredWidth();
            }
        });
        // 在xml文件里设置的半径
        radius = UiUtils.dp2px(this, 80);
    }

    private void initWidget() {
        mAdd = findViewById(R.id.fab_add);
        mLike = findViewById(R.id.fab_like);
        mTop = findViewById(R.id.fab_top);
        mWrite = findViewById(R.id.fab_write);
        likeGroup = findViewById(R.id.gp_like);
        writeGroup = findViewById(R.id.gp_write);
        topGroup = findViewById(R.id.gp_top);
        // 将三个弹出的FloatingActionButton隐藏
        setViewVisible(false);
    }

    private void setViewVisible(boolean isShow) {
        likeGroup.setVisibility(isShow?View.VISIBLE:View.GONE);
        writeGroup.setVisibility(isShow?View.VISIBLE:View.GONE);
        topGroup.setVisibility(isShow?View.VISIBLE:View.GONE);
    }
```
我们的重点就在`initListener()`里面，思路就是利用属性动画控制`ConstraintLayout.LayoutParams`，从而控制`Circular positioning`的角度和半径：
```
    private void initListener() {
        mAdd.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // 播放动画的时候不可以点击
                if(animatorSet != null && animatorSet.isRunning())
                    return;

                // 判断播放显示还是隐藏动画
                if(likeGroup.getVisibility() != View.VISIBLE) {
                    animatorSet = new AnimatorSet();
                    ValueAnimator likeAnimator = getValueAnimator(mLike, false, likeGroup,0);
                    ValueAnimator writeAnimator = getValueAnimator(mWrite, false, writeGroup,45);
                    ValueAnimator topAnimator = getValueAnimator(mTop, false, topGroup,90);
                    animatorSet.playSequentially(likeAnimator, writeAnimator, topAnimator);
                    animatorSet.start();
                }else {
                    animatorSet = new AnimatorSet();
                    ValueAnimator likeAnimator = getValueAnimator(mLike, true, likeGroup,0);
                    ValueAnimator writeAnimator = getValueAnimator(mWrite, true, writeGroup,45);
                    ValueAnimator topAnimator = getValueAnimator(mTop, true, topGroup,90);
                    animatorSet.playSequentially(topAnimator, writeAnimator, likeAnimator);
                    animatorSet.start();
                }

            }
        });
    }

    /**
     * 获取ValueAnimator
     * 
     * @param button FloatingActionButton
     * @param reverse 开始还是隐藏
     * @param group Group
     * @param angle angle 转动的角度
     * @return ValueAnimator
     */
    private ValueAnimator getValueAnimator(final FloatingActionButton button, final boolean reverse, final Group group, final int angle) {
        ValueAnimator animator;
        if (reverse)
            animator = ValueAnimator.ofFloat(1, 0);
        else
            animator = ValueAnimator.ofFloat(0, 1);
        animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
            @Override
            public void onAnimationUpdate(ValueAnimator animation) {
                float v = (float) animation.getAnimatedValue();
                ConstraintLayout.LayoutParams params = (ConstraintLayout.LayoutParams) button.getLayoutParams();
                params.circleRadius = (int) (radius * v);
                //params.circleAngle = 270f + angle * v;
                params.width = (int) (width * v);
                params.height = (int) (width * v);
                button.setLayoutParams(params);
            }
        });
        animator.addListener(new SimpleAnimation() {
            @Override
            public void onAnimationStart(Animator animation) {
                group.setVisibility(View.VISIBLE);
            }

            @Override
            public void onAnimationEnd(Animator animation) {
                if(group == likeGroup && reverse){
                    setViewVisible(false);
                }
            }
        });
        animator.setDuration(300);
        animator.setInterpolator(new DecelerateInterpolator());
        return animator;
    }

    abstract class SimpleAnimation implements Animator.AnimatorListener{
        @Override
        public void onAnimationStart(Animator animation) {
        }

        @Override
        public void onAnimationEnd(Animator animation) {
        }

        @Override
        public void onAnimationCancel(Animator animation) {
        }

        @Override
        public void onAnimationRepeat(Animator animation) {
        }
    }
```
这样写完效果就出来了：
![效果](https://upload-images.jianshu.io/upload_images/9271486-c28b9538e9313114.gif?imageMogr2/auto-orient/strip)
如果你觉得弹出的曲线不够圆滑，你可以在`getValueAnimator`方法中取消对`//params.circleAngle = 270f + angle * v;`这行的注释，效果就如本章一开始的效果。
## 四. 总结
本文的思路就是利用属性动画控制`ConstraintLayout.LayoutParams`，从而控制`Circular positioning`的角度和半径，内容比较简单，前提是你得掌握属性动画和`ConstraintLayout`的使用。本人水平有限，难免有误，如有错误，欢迎提出。
[Demo地址](https://github.com/mCyp/Test)
Over~






