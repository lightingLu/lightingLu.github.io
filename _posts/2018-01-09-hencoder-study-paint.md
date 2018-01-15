---
layout:     post
title:      "HenCode学习—Paint使用"
subtitle:   "aint"
date:       2018-01-13
author:     "light"
header-img: ""
catalog:    true
tags:
    - 学习
    - Hencoder
    - 自定义控件
    - 绘图

---

# HenCoder 学习—canvas 的使用

今天总结的是第二章，主要讲paint的使用。paint可以设置更多的值。

## 1.总述

 `Paint` 的 API 大致可以分为 4 类

- 颜色
- 效果
- drawText() 相关
- 初始化

## 2.颜色

颜色的处理有三种：

![颜色处理](https://ws3.sinaimg.cn/large/52eb2279ly1fig6dcywn2j20j909yabu.jpg)



#### 2.1 基本颜色

![](https://ws3.sinaimg.cn/large/52eb2279ly1fig6gxcusnj20iw04xmzr.jpg)

`Paint` 设置颜色的方法有两种：一种是直接用 `Paint.setColor/ARGB()` 来设置颜色，另一种是使用 `Shader` 来指定着色方案。

#### 2.1.1 直接设置

* setColor(int color)

* setARGB(int a, int r, int g, int b)

  ```Java
  paint.setColor(Color.parseColor("#FF9800"));  
  canvas.drawLine(300, 30, 450, 180, paint);
  paint.setARGB(100, 0, 0, 0);  
  canvas.drawLine(0, 0, 200, 200, paint);  
  ```

  #### 2.1.2 setShader(Shader shader) 设置 Shader

  > Shader 这个英文单词很多人没有见过，它的中文叫做「着色器」，也是用于设置绘制颜色的。「着色器」不是 Android 独有的，它是图形领域里一个通用的概念，它和直接设置颜色的区别是，着色器设置的是一个颜色方案，或者说是一套着色规则。当设置了 `Shader` 之后，`Paint` 在绘制图形和文字时就不使用 `setColor/ARGB()` 设置的颜色了，而是使用 `Shader` 的方案中的颜色
  >
  > 注意：在设置了 `Shader` 的情况下， `Paint.setColor/ARGB()` 所设置的颜色就不再起作用。

  * LinearGradient 线性渐变

    参数： 
    `x0` `y0` `x1` `y1`：渐变的两个端点的位置 
    `color0` `color1` 是端点的颜色 
    `tile`：端点范围之外的着色规则，类型是 `TileMode`。`TileMode` 一共有 3 个值可选： `CLAMP`, `MIRROR`和 `REPEAT`

  ```Java
  Shader shader = new LinearGradient(100, 100, 500, 500, Color.parseColor("#E91E63"),  
          Color.parseColor("#2196F3"), Shader.TileMode.CLAMP);
  paint.setShader(shader);
  canvas.drawCircle(300, 300, 200, paint);  
  ```

  * RadialGradient 辐射渐变

    构造方法： 
    `RadialGradient(float centerX, float centerY, float radius, int centerColor, int edgeColor, TileMode tileMode)`。

    参数： 
    `centerX` `centerY`：辐射中心的坐标 
    `radius`：辐射半径 
    `centerColor`：辐射中心的颜色 
    `edgeColor`：辐射边缘的颜色 
    `tileMode`：辐射范围之外的着色模式。

    ```Java
    Shader shader = new RadialGradient(300, 300, 200, Color.parseColor("#E91E63"),  
            Color.parseColor("#2196F3"), Shader.TileMode.CLAMP);
    paint.setShader(shader);
    canvas.drawCircle(300, 300, 200, paint); 
    ```

    ​

  * SweepGradient 扫描渐变

    构造方法： 
    `SweepGradient(float cx, float cy, int color0, int color1)`

    参数： 
    `cx` `cy` ：扫描的中心 
    `color0`：扫描的起始颜色 
    `color1`：扫描的终止颜色

    ​

    ```jAVA
    Shader shader = new SweepGradient(300, 300, Color.parseColor("#E91E63"),  
            Color.parseColor("#2196F3"));
    paint.setShader(shader);
    canvas.drawCircle(300, 300, 200, paint);  
    ```

    ​

  * BitmapShader

    > 其实也就是用 `Bitmap` 的像素来作为图形或文字的填充。

    ![](https://ws3.sinaimg.cn/large/52eb2279ly1fig6fragq2j20lc089djv.jpg)

    构造方法： 
    `BitmapShader(Bitmap bitmap, Shader.TileMode tileX, Shader.TileMode tileY)`

    参数： 
    `bitmap`：用来做模板的 `Bitmap` 对象 
    `tileX`：横向的 `TileMode` 
    `tileY`：纵向的 `TileMode`。

         ```JAVA
  Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.batman);  
  Shader shader = new BitmapShader(bitmap, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);  
  paint.setShader(shader);
  canvas.drawCircle(300, 300, 200, paint);  
         ```

  * ComposeShader 混合着色器

    > 所谓混合，就是把两个 `Shader` 一起使用。

    构造方法：`ComposeShader(Shader shaderA, Shader shaderB, PorterDuff.Mode mode)`

    参数： 
    `shaderA`, `shaderB`：两个相继使用的 `Shader` 
    `mode`: 两个 `Shader` 的叠加模式，即 `shaderA` 和 `shaderB` 应该怎样共同绘制。它的类型是 `PorterDuff.Mode` 。

    > PorterDuff.Mode
    >
    > `PorterDuff.Mode` 是用来指定两个图像共同绘制时的颜色策略的。它是一个 enum，不同的 `Mode` 可以指定不同的策略。「颜色策略」的意思，就是说把源图像绘制到目标图像处时应该怎样确定二者结合后的颜色，而对于 `ComposeShader(shaderA, shaderB, mode)` 这个具体的方法，就是指应该怎样把 `shaderB` 绘制在 `shaderA` 上来得到一个结合后的 `Shader`。

    ![](https://ws3.sinaimg.cn/large/52eb2279ly1fig6im3hhcj20o50zt7bj.jpg)

    ```JAVA
    // 第一个 Shader：头像的 Bitmap
    Bitmap bitmap1 = BitmapFactory.decodeResource(getResources(), R.drawable.batman);  
    Shader shader1 = new BitmapShader(bitmap1, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);

    // 第二个 Shader：从上到下的线性渐变（由透明到黑色）
    Bitmap bitmap2 = BitmapFactory.decodeResource(getResources(), R.drawable.batman_logo);  
    Shader shader2 = new BitmapShader(bitmap2, Shader.TileMode.CLAMP, Shader.TileMode.CLAMP);

    // ComposeShader：结合两个 Shader
    Shader shader = new ComposeShader(shader1, shader2, PorterDuff.Mode.SRC_OVER);  
    paint.setShader(shader);
    canvas.drawCircle(300, 300, 300, paint); 
    ```

    > 注意：上面这段代码中我使用了两个 `BitmapShader` 来作为 `ComposeShader()` 的参数，而 `ComposeShader()` 在硬件加速下是不支持两个相同类型的 `Shader` 的，所以这里也需要关闭硬件加速才能看到效果。

    ![](https://ws3.sinaimg.cn/large/52eb2279ly1fig6hbeg7gj20qy08cafn.jpg)

  ​

  ### 2.2 setColorFilter(ColorFilter colorFilter)

  `ColorFilter` 这个类，它的名字已经足够解释它的作用：为绘制设置颜色过滤。颜色过滤的意思，就是为绘制的内容设置一个统一的过滤策略，然后 `Canvas.drawXXX()` 方法会对每个像素都进行过滤后再绘制出来

* `Paint.setColorFilter(ColorFilter filter)` 方法。 `ColorFilter` 并不直接使用，而是使用它的子类。它共有三个子类：`LightingColorFilter``PorterDuffColorFilter` 和 `ColorMatrixColorFilter`。

  * LightingColorFilter 模拟简单的光照效果

    > LightingColorFilter` 的构造方法是 `LightingColorFilter(int mul, int add)` ，参数里的 `mul` 和 `add` 都是和颜色值格式相同的 int 值，其中 `mul` 用来和目标像素相乘，`add` 用来和目标像素相加

    ```JAVA
    ColorFilter lightingColorFilter = new LightingColorFilter(0x00ffff, 0x000000);  
    paint.setColorFilter(lightingColorFilter);  
    ```

    * PorterDuffColorFilter

      这个 `PorterDuffColorFilter` 的作用是使用一个指定的颜色和一种指定的 `PorterDuff.Mode` 来与绘制对象进行合成。它的构造方法是 `PorterDuffColorFilter(int color, PorterDuff.Mode mode)` 其中的 `color` 参数是指定的颜色， `mode` 参数是指定的 `Mode`。同样也是 `PorterDuff.Mode` ，不过和 `ComposeShader` 不同的是，`PorterDuffColorFilter` 作为一个 `ColorFilter`，只能指定一种颜色作为源，而不是一个 `Bitmap`


    * ColorMatrixColorFilter

      `ColorMatrixColorFilter` 使用一个 `ColorMatrix` 来对颜色进行处理。 `ColorMatrix`这个类，内部是一个 4x5 的矩阵

      ```JAVA

             private float[] mColorMatrix = new float[20];
             final float[] a = mColorMatrix;
              Arrays.fill(a, 0);
              a[0] = a[6] = a[12] = a[18] = 1;
               reset();
              bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.batman);
              ColorMatrix matrix =new ColorMatrix();
              matrix.set(mColorMatrix);
              ColorFilter filter =new ColorMatrixColorFilter(matrix);
              paint.setColorFilter(filter);
              canvas.drawBitmap(bitmap, 0, 0, paint);

      ```

    ​

  ### 1.3 setXfermode(Xfermode xfermode)

  "Xfermode" 其实就是 "Transfer mode"，用 "X" 来代替 "Trans" 是一些美国人喜欢用的简写方式。严谨地讲， `Xfermode` 指的是你要绘制的内容和 `Canvas` 的目标位置的内容应该怎样结合计算出最终的颜色。但通俗地说，其实就是要你以绘制的内容作为源图像，以 View 中已有的内容作为目标图像，选取一个 `PorterDuff.Mode` 作为绘制内容的颜色处理方案

  ```JAVA
  Xfermode xfermode = new PorterDuffXfermode(PorterDuff.Mode.DST_IN);
  canvas.drawBitmap(rectBitmap, 0, 0, paint); // 画方  
  paint.setXfermode(xfermode); // 设置 Xfermode  
  canvas.drawBitmap(circleBitmap, 0, 0, paint); // 画圆  
  paint.setXfermode(null); // 用完及时清除 Xfermode  
  ```

  ![](https://ws1.sinaimg.cn/large/006tNc79ly1fig6x8dhk6j30nl0pswff.jpg)

> `PorterDuff.Mode` 在 `Paint` 一共有三处 API ，它们的工作原理都一样，只是用途不同：

![](https://ws2.sinaimg.cn/large/006tNc79ly1fig70nh3gpj30fs04uwez.jpg)

#### Xfermode注意事项

* 使用离屏缓冲（Off-screen Buffer）

* Canvas.saveLayer()

  `saveLayer()` 可以做短时的离屏缓冲。使用方法很简单，在绘制代码的前后各加一行代码，在绘制之前保存，绘制之后恢复：

  ```Java
  int saved = canvas.saveLayer(null, null, Canvas.ALL_SAVE_FLAG);
  canvas.drawBitmap(rectBitmap, 0, 0, paint); // 画方
  paint.setXfermode(xfermode); // 设置 Xfermode
  canvas.drawBitmap(circleBitmap, 0, 0, paint); // 画圆
  paint.setXfermode(null); // 用完及时清除 Xfermode
  canvas.restoreToCount(saved);

  ```

  * `View.setLayerType()`

    `View.setLayerType()` 是直接把整个 `View` 都绘制在离屏缓冲中。 

    `setLayerType(LAYER_TYPE_HARDWARE)` 是使用 GPU 来缓冲， 

    `setLayerType(LAYER_TYPE_SOFTWARE)` 是直接直接用一个 `Bitmap` 来缓

    ​

  ## 3 效果

  ### 3.1 setAntiAlias (boolean aa) 设置抗锯齿

  ### 3.2 setStyle(Paint.Style style)

  ### 3.3 线条形状

  #### 3.3.1 setStrokeWidth(float width)

  #### 3.3.2 setStrokeCap(Paint.Cap cap)

  设置线头的形状。线头形状有三种：`BUTT` 平头、`ROUND` 圆头、`SQUARE` 方头。默认为 `BUTT`。

  #### 3.3.3 setStrokeJoin(Paint.Join join)

  设置拐角的形状。有三个值可以选择：`MITER` 尖角、 `BEVEL` 平角和 `ROUND` 圆角。默认为 `MITER`。

  #### 3.3.4 setStrokeMiter(float miter)

  这个方法是对于 `setStrokeJoin()` 的一个补充，它用于设置 `MITER` 型拐角的延长线的最大值。所谓「延长线的最大值」

  ### 3.4 色彩优化

  `Paint` 的色彩优化有两个方法： `setDither(boolean dither)` 和 `setFilterBitmap(boolean filter)`。它们的作用都是让画面颜色变得更加「顺眼」，但原理和使用场景是不同的。

  #### 3.4.1 setDither(boolean dither) 设置图像的抖动

  ```Java
  paint.setDither(true);  
  ```

  #### 3.4.2 setFilterBitmap(boolean filter) 置是否使用双线性过滤来绘制 `Bitmap`

  图像在放大绘制的时候，默认使用的是最近邻插值过滤，这种算法简单，但会出现马赛克现象；而如果开启了双线性过滤，就可以让结果图像显得更加平滑。

  ```Java
  paint.setFilterBitmap(true);  
  ```

  ### 3.5 setPathEffect(PathEffect effect)

  使用 `PathEffect` 来给图形的轮廓设置效果。对 `Canvas` 所有的图形绘制有效，也就是 `drawLine()``drawCircle()` `drawPath()` 这些方法

  ```java
  PathEffect pathEffect = new DashPathEffect(new float[]{10, 5}, 10);  
  paint.setPathEffect(pathEffect);
  canvas.drawCircle(300, 300, 200, paint); 
  ```

  ![](https://ws4.sinaimg.cn/large/006tNc79ly1fig7diyn5jj308h089mx5.jpg)

  Android 中的 6 种 `PathEffect`。`PathEffect` 分为两类，单一效果的 `CornerPathEffect` `DiscretePathEffect` `DashPathEffect` `PathDashPathEffect` ，和组合效果的 `SumPathEffect` `ComposePathEffect`

#### 3.5.1 CornerPathEffect   把所有拐角变成圆角

```JAVA
paint.setFilterBitmap(true);  
```

![](https://ws1.sinaimg.cn/large/006tNc79ly1fig7dobrizj30iv0agt8z.jpg)

#### 3.5.2 DiscretePathEffect

把线条进行随机的偏离，让轮廓变得乱七八糟。乱七八糟的方式和程度由参数决定。

```JAVA
PathEffect pathEffect = new DiscretePathEffect(20, 5);  
paint.setPathEffect(pathEffect);
canvas.drawPath(path, paint); 
```

![](https://ws2.sinaimg.cn/large/006tNc79ly1fig7dug01cj30ir0bawet.jpg)

`DiscretePathEffect` 具体的做法是，把绘制改为使用定长的线段来拼接，并且在拼接的时候对路径进行随机偏离。它的构造方法 `DiscretePathEffect(float segmentLength, float deviation)` 的两个参数中， `segmentLength` 是用来拼接的每个线段的长度， `deviation` 是偏离量

#### 3.5.3 DashPathEffect

使用虚线来绘制线条。

```JAVA
PathEffect pathEffect = new DashPathEffect(new float[]{20, 10, 5, 10}, 0);  
paint.setPathEffect(pathEffect);
canvas.drawPath(path, paint);  
```

![](https://ws4.sinaimg.cn/large/006tNc79ly1fig7dz1jenj30iw0b9mxh.jpg)

它的构造方法 `DashPathEffect(float[] intervals, float phase)` 中， 第一个参数 `intervals` 是一个数组，它指定了虚线的格式：数组中元素必须为偶数（最少是 2 个），按照「画线长度、空白长度、画线长度、空白长度」……的顺序排列，例如上面代码中的 `20, 5, 10, 5` 就表示虚线是按照「画 20 像素、空 5 像素、画 10 像素、空 5 像素」的模式来绘制；第二个参数 `phase` 是虚线的偏移量。

#### 3.5.4 PathDashPathEffect

这个方法比 `DashPathEffect` 多一个前缀 `Path` ，所以顾名思义，它是使用一个 `Path` 来绘制「虚线」。具体看图吧：

```JAVA
Path dashPath = ...; // 使用一个三角形来做 dash  
PathEffect pathEffect = new PathDashPathEffect(dashPath, 40, 0,  
        PathDashPathEffectStyle.TRANSLATE);
paint.setPathEffect(pathEffect);
canvas.drawPath(path, paint);  
```

![img](https://ws2.sinaimg.cn/large/006tNc79ly1fig7e4xp38j30j30bc0t7.jpg)

它的构造方法 `PathDashPathEffect(Path shape, float advance, float phase, PathDashPathEffect.Style style)`中， `shape` 参数是用来绘制的 `Path` ； `advance` 是两个相邻的 `shape` 段之间的间隔，不过注意，这个间隔是两个 `shape` 段的起点的间隔，而不是前一个的终点和后一个的起点的距离； `phase` 和 `DashPathEffect` 中一样，是虚线的偏移；最后一个参数 `style`，是用来指定拐弯改变的时候 `shape` 的转换方式。`style` 的类型为 `PathDashPathEffect.Style` ，是一个 `enum` ，具体有三个值：

- `TRANSLATE`：位移
- `ROTATE`：旋转
- `MORPH`：变体

![img](https://ws1.sinaimg.cn/large/006tNc79ly1fig7efqw9qj30kn0h3dh5.jpg)

#### 3.5.5 SumPathEffect

这是一个组合效果类的 `PathEffect` 。它的行为特别简单，就是分别按照两种 `PathEffect` 分别对目标进行绘制。

```JAVA
PathEffect dashEffect = new DashPathEffect(new float[]{20, 10}, 0);  
PathEffect discreteEffect = new DiscretePathEffect(20, 5);  
pathEffect = new SumPathEffect(dashEffect, discreteEffect);
canvas.drawPath(path, paint);  

```

![img](https://ws1.sinaimg.cn/large/006tNc79ly1fig7ekjh7lj30dw05jq2z.jpg)

#### 3.5.6 ComposePathEffect

这也是一个组合效果类的 `PathEffect` 。不过它是先对目标 `Path` 使用一个 `PathEffect`，然后再对这个改变后的 `Path` 使用另一个 `PathEffect`。

```JAVA
PathEffect dashEffect = new DashPathEffect(new float[]{20, 10}, 0);  
PathEffect discreteEffect = new DiscretePathEffect(20, 5);  
pathEffect = new ComposePathEffect(dashEffect, discreteEffect);
canvas.drawPath(path, paint);  

```

![img](https://ws3.sinaimg.cn/large/006tNc79ly1fig7epf94aj30dr05eq2x.jpg)

它的构造方法 `ComposePathEffect(PathEffect outerpe, PathEffect innerpe)` 中的两个 `PathEffect` 参数， `innerpe` 是先应用的， `outerpe` 是后应用的。所以上面的代码就是「先偏离，再变虚线」。

> 注意： `PathEffect` 在有些情况下不支持硬件加速，需要关闭硬件加速才能正常使用：
>
> 1.Canvas.drawLine()` 和 `Canvas.drawLines()` 方法画直线时，`setPathEffect()` 是不支持硬件加速的；2.PathDashPathEffect` 对硬件加速的支持也有问题，所以当使用 `PathDashPathEffect` 的时候，最好也把硬件加速关了

### 3.6 setShadowLayer(float radius, float dx, float dy, int shadowColor)

在之后的绘制内容下面加一层阴影。

```
paint.setShadowLayer(10, 0, 0, Color.RED);

...

canvas.drawText(text, 80, 300, paint);  









```

![img](https://ws4.sinaimg.cn/large/006tNc79ly1fig7ev8io8j30cv02y74f.jpg)

效果就是上面这样。方法的参数里， `radius` 是阴影的模糊范围； `dx` `dy` 是阴影的偏移量； `shadowColor` 是阴影的颜色。

如果要清除阴影层，使用 `clearShadowLayer()` 。

注意：

- 在硬件加速开启的情况下， `setShadowLayer()` 只支持文字的绘制，文字之外的绘制必须关闭硬件加速才能正常绘制阴影。
- 如果 `shadowColor` 是半透明的，阴影的透明度就使用 `shadowColor` 自己的透明度；而如果 `shadowColor` 是不透明的，阴影的透明度就使用 `paint` 的透明度。

### 3.7 setMaskFilter(MaskFilter maskfilter)

为之后的绘制设置 `MaskFilter`。上一个方法 `setShadowLayer()` 是设置的在绘制层下方的附加效果；而这个 `MaskFilter` 和它相反，设置的是在绘制层上方的附加效果。

> 到现在已经有两个 `setXxxFilter(filter)` 了。前面有一个 `setColorFilter(filter)` ，是对每个像素的颜色进行过滤；而这里的 `setMaskFilter(filter)` 则是基于整个画面来进行过滤。

`MaskFilter` 有两种： `BlurMaskFilter` 和 `EmbossMaskFilter`。

#### 3.7.1 BlurMaskFilter

模糊效果的 `MaskFilter`。

```JAVA
paint.setMaskFilter(new BlurMaskFilter(50, BlurMaskFilter.Blur.NORMAL));

canvas.drawBitmap(bitmap, 100, 100, paint);  
```

它的构造方法 `BlurMaskFilter(float radius, BlurMaskFilter.Blur style)` 中， `radius` 参数是模糊的范围， `style` 是模糊的类型。一共有四种：

- `NORMAL`: 内外都模糊绘制
- `SOLID`: 内部正常绘制，外部模糊
- `INNER`: 内部模糊，外部不绘制
- `OUTER`: 内部不绘制，外部模糊

#### 3.7.2 EmbossMaskFilter

浮雕效果的 `MaskFilter`。

```JAVA
paint.setMaskFilter(new EmbossMaskFilter(new float[]{0, 1, 1}, 0.2f, 8, 10));

canvas.drawBitmap(bitmap, 100, 100, paint);  
```

### 3.8 获取绘制的实际 Path

#### 3.8.1 getFillPath(Path src, Path dst)

#### 3.8.2 getTextPath(String text, int start, int end, float x, float y, Path path) / getTextPath(char[] text, int index, int count, float x, float y, Path path)

* ​
