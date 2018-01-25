---
layout:     post
title:      “Hencoder学习—绘制顺序x"
subtitle:   "draw,ondraw"
date:       2018-01-19
author:     "light"
header-img: ""
catalog:    true
tags:
    - 学习
    - 自定义View

---

###Hencoder学习—绘制顺序

Android 里面的绘制都是按顺序的，先绘制的内容会被后绘制的盖住

### 1 super.onDraw()

绘制代码写在  super.onDraw() 的上面还是下面都无所谓，甚至，你把 super.onDraw() 这行代码删掉都没关系，效果都是一样的——因为在 View 这个类里，onDraw() 本来就是空实现：

### 1.1 写在 super.onDraw() 的下面
把绘制代码写在 super.onDraw() 的下面，由于绘制代码会在原有内容绘制结束之后才执行，所以绘制内容就会盖住控件原来的内容。

```java
public class AppImageView extends ImageView {  
    ...

    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        if (DEBUG) {
            // 在 debug 模式下绘制出 drawable 的尺寸信息
            ...
        }
    }
}
```

### 1.2 写在 super.onDraw() 的上面

写在 `super.onDraw()` 的上面，由于绘制代码会执行在原有内容的绘制之前，所以绘制的内容会被控件的原内容盖住

```java
public class AppTextView extends TextView {  
    ...

    protected void onDraw(Canvas canvas) {
        ... // 在 super.onDraw() 绘制文字之前，先绘制出被强调的文字的背景

        super.onDraw(canvas);
    }
}
```

## 2 dispatchDraw()：绘制子 View 的方法

绘制子 View」是通过另一个绘制方法的调用来发生的，这个绘制方法叫做：`dispatchDraw()`。在绘制过程中，每个 View 和 ViewGroup 都会先调用 `onDraw()` 方法来绘制主体，再调用 `dispatchDraw()` 方法来绘制子 View

> 注：虽然 `View` 和 `ViewGroup` 都有 `dispatchDraw()` 方法，不过由于 View 是没有子 View 的，所以一般来说 `dispatchDraw()` 这个方法只对 `ViewGroup`（以及它的子类）有意义。

### 2.1 写在 super.dispatchDraw() 的下面

只要重写 `dispatchDraw()`，并在 `super.dispatchDraw()` 的下面写上你的绘制代码，这段绘制代码就会发生在子 View 的绘制之后，从而让绘制内容盖住子 View 了。

### 2.2 写在 super.dispatchDraw() 的上面

绘制内容会出现在主体内容和子 View 之间

## 3 绘制过程简述

。具体来讲，一个完整的绘制过程会依次绘制以下几个内容：

1. 背景
2. 主体（`onDraw()`）
3. 子 View（`dispatchDraw()`）
4. 滑动边缘渐变和滑动条
5. 前景

第 1 步——背景，它的绘制发生在一个叫 `drawBackground()` 的方法里，但这个方法是 `private` 的，不能重写，你如果要设置背景，只能用自带的 API 去设置（xml 布局文件的 `android:background` 属性以及 Java 代码的 `View.setBackgroundXxx()` 方法，这个每个人都用得很 6 了），而不能自定义绘制；而第 4、5 两步——滑动边缘渐变和滑动条以及前景，这两部分被合在一起放在了 `onDrawForeground()` 方法里，这个方法是可以重写的。

## 4 onDrawForeground()

 `onDrawForeground()` 中，会依次绘制滑动边缘渐变、滑动条和前景。所以如果你重写 `onDrawForeground()

### 4.1 写在 super.onDrawForeground() 的下面

绘制代码会在滑动边缘渐变、滑动条和前景之后被执行，那么绘制内容将会盖住滑动边缘渐变、滑动条和前景。

### 4.2 写在 super.onDrawForeground() 的上面

绘制内容就会在 `dispatchDraw()` 和 `super.onDrawForeground()` 之间执行，那么绘制内容会盖住子 View，但被滑动边缘渐变、滑动条以及前景盖住

### 4.3 想在滑动边缘渐变、滑动条和前景之间插入绘制代码？

虽然这三部分是依次绘制的，但它们被一起写进了 `onDrawForeground()` 方法里，所以你要么把绘制内容插在它们之前，要么把绘制内容插在它们之后。而想往它们之间插入绘制，是做不到的。

## 5 draw() 总调度方法

draw() 是绘制过程的总调度方法。一个 View 的整个绘制过程都发生在 `draw()` 方法里。前面讲到的背景、主体、子 View 、滑动相关以及前景的绘制，它们其实都是在 `draw()` 方法里的

```java
// View.java 的 draw() 方法的简化版大致结构（是大致结构，不是源码哦）：

public void draw(Canvas canvas) {  
    ...

    drawBackground(Canvas); // 绘制背景（不能重写）
    onDraw(Canvas); // 绘制主体
    dispatchDraw(Canvas); // 绘制子 View
    onDrawForeground(Canvas); // 绘制滑动相关和前景

    ...
}
```

### 5.1 写在 super.draw() 的下面

这段代码会在其他所有绘制完成之后再执行，也就是说，它的绘制内容会盖住其他的所有绘制内容

### 5.2 写在 super.draw() 的上面

这部分绘制内容会被其他所有的内容盖住，包括背景

## 注意

关于绘制方法，有两点需要注意一下：

1. 出于效率的考虑，`ViewGroup` 默认会绕过 `draw()` 方法，换而直接执行 `dispatchDraw()`，以此来简化绘制流程。所以如果你自定义了某个 `ViewGroup` 的子类（比如 `LinearLayout`）并且需要在它的除 `dispatchDraw()` 以外的任何一个绘制方法内绘制内容，你**可能**会需要调用 `View.setWillNotDraw(false)`这行代码来切换到完整的绘制流程（是「可能」而不是「必须」的原因是，有些 ViewGroup 是已经调用过 `setWillNotDraw(false)` 了的，例如 `ScrollView`）。
2. 有的时候，一段绘制代码写在不同的绘制方法中效果是一样的，这时你可以选一个自己喜欢或者习惯的绘制方法来重写。但有一个例外：如果绘制代码既可以写在 `onDraw()` 里，也可以写在其他绘制方法里，那么优先写在 `onDraw()` ，因为 Android 有相关的优化，可以在不需要重绘的时候自动跳过 `onDraw()` 的重复执行，以提升开发效率。享受这种优化的只有 `onDraw()` 一个方法。





