---
layout:     post
title:      “Hencoder学习—动画的使用"
subtitle:   "animation"
date:       2018-01-19
author:     "light"
header-img: ""
catalog:    true
tags:
    - 学习
    - 自定义View

---

###Hencoder学习—属性动画
### 一、基础篇
动画可以分为两类：Animation 和 Transition；其中 Animation 又可以再分为 View Animation 和 Property Animation 两类。

ViewPropertyAnimator

使用方式：View.animate() 后跟 translationX() 等方法，动画会自动执行。

view.animate().translationX(500);  

#### ObjectAnimator
使用方式：

如果是自定义控件，需要添加 setter / getter 方法；
用 ObjectAnimator.ofXXX() 创建 ObjectAnimator 对象；
用 start() 方法执行动画。
* 1. setDuration(int duration) 设置动画时长
* 2. setInterpolator(Interpolator interpolator) 设置 Interpolator


#### 设置监听器
ViewPropertyAnimator.setListener() / ObjectAnimator.addListener()

### 二、进阶篇

#### ArgbEvaluator


如视频中的例子，TypeEvaluator 最经典的用法是使用 ArgbEvaluator 来做颜色渐变的动画。
```java
ObjectAnimator animator = ObjectAnimator.ofInt(view, "color", 0xffff0000, 0xff00ff00);  
animator.setEvaluator(new ArgbEvaluator());  
animator.start(); 
```
Android 5.0 （API 21） 加入了新的方法 ofArgb()，所以如果你的 minSdk 大于或者等于 21（哈哈哈哈哈哈哈哈），你可以直接用下面这种方式：
```java
ObjectAnimator animator = ObjectAnimator.ofArgb(view, "color", 0xffff0000, 0xff00ff00);  
animator.start();  
```
#### PropertyValuesHolder 同一个动画中改变多个属性
* 第一种view 本身属性
```java
view.animate()  
        .scaleX(1)
        .scaleY(1)
        .alpha(1);
```
* 第二种PropertyValuesHolder 
```java
PropertyValuesHolder holder1 = PropertyValuesHolder.ofFloat("scaleX", 1);  
PropertyValuesHolder holder2 = PropertyValuesHolder.ofFloat("scaleY", 1);  
PropertyValuesHolder holder3 = PropertyValuesHolder.ofFloat("alpha", 1);
ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(view, holder1, holder2, holder3)  
animator.start();  
```
* 第三种AnimatorSet 
```java
ObjectAnimator animator1 = ObjectAnimator.ofFloat(...);  
animator1.setInterpolator(new LinearInterpolator());  
ObjectAnimator animator2 = ObjectAnimator.ofInt(...);  
animator2.setInterpolator(new DecelerateInterpolator());
AnimatorSet animatorSet = new AnimatorSet();  
// 两个动画依次执行
animatorSet.playSequentially(animator1, animator2);  
animatorSet.start();  
```
* 第四种 PropertyValuesHolders.ofKeyframe()

```java
// 在 0% 处开始
Keyframe keyframe1 = Keyframe.ofFloat(0, 0);  
// 时间经过 50% 的时候，动画完成度 100%
Keyframe keyframe2 = Keyframe.ofFloat(0.5f, 100);  
// 时间见过 100% 的时候，动画完成度倒退到 80%，即反弹 20%
Keyframe keyframe3 = Keyframe.ofFloat(1, 80);  
PropertyValuesHolder holder = PropertyValuesHolder.ofKeyframe("progress", keyframe1, keyframe2, keyframe3);
ObjectAnimator animator = ObjectAnimator.ofPropertyValuesHolder(view, holder);  
animator.start();  
````

> 使用 PropertyValuesHolder 来对多个属性同时做动画；
使用 AnimatorSet 来同时管理调配多个动画；
PropertyValuesHolder 的进阶使用：使用 PropertyValuesHolder.ofKeyframe() 来把一个属性拆分成多段，执行更加精细的属性动画。









