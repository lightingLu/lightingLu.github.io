---
layout:     post
title:      “Hencoder学习—clip和Matrix"
subtitle:   "Camera和Matrix使用"
date:       2018-01-19
author:     "light"
header-img: ""
catalog:    true
tags:
    - 学习
    - 自定义View

---

###Hencoder学习—clip和Matrix

matrix和Camera可以更多我们意想不到的动画。

### 1 范围裁切

#### 1.1范围裁切有两个方法： `clipRect()` 和 `clipPath()`

```java
canvas.save();  
canvas.clipRect(left, top, right, bottom);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore();   
```

### 1.2 clipPath()

```Java
canvas.save();  
canvas.clipPath(path1);  
canvas.drawBitmap(bitmap, point1.x, point1.y, paint);  
canvas.restore();

canvas.save();  
canvas.clipPath(path2);  
canvas.drawBitmap(bitmap, point2.x, point2.y, paint);  
canvas.restore();
```

## 2 几何变换

### 2.1 使用 Canvas 来做常见的二维变换：

#### 2.1.1 Canvas.translate(float dx, float dy) 平移  

移动canvas和控件看起来刚好相反

#### 2.1.2 Canvas.rotate(float degrees, float px, float py) 旋转

```java
canvas.save();  
canvas.rotate(45, centerX, centerY);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore();
```

#### 2.1.3 Canvas.scale(float sx, float sy, float px, float py) 放缩

参数里的 `sx` `sy` 是横向和纵向的放缩倍数； `px` `py` 是放缩的轴心。

```Java
canvas.save();  
canvas.scale(1.3f, 1.3f, x + bitmapWidth / 2, y + bitmapHeight / 2);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore();  
```

#### 2.1.4 skew(float sx, float sy) 错切

参数里的 `sx` 和 `sy` 是 x 方向和 y 方向的错切系数。

```Java
canvas.save();  
canvas.scale(1.3f, 1.3f, x + bitmapWidth / 2, y + bitmapHeight / 2);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore(); 
```

### 2.2 使用 Matrix 来做变换

`Matrix` 做常见变换的方式：

1. 创建 `Matrix` 对象；
2. 调用 `Matrix` 的 `pre/postTranslate/Rotate/Scale/Skew()` 方法来设置几何变换；
3. 使用 `Canvas.setMatrix(matrix)` 或 `Canvas.concat(matrix)` 来把几何变换应用到 `Canvas`。

```Java
Matrix matrix = new Matrix();
matrix.reset();  
matrix.postTranslate();  
matrix.postRotate();

canvas.save();  
canvas.concat(matrix);  
canvas.drawBitmap(bitmap, x, y, paint);  
canvas.restore();  
```

把 `Matrix` 应用到 `Canvas` 有两个方法： `Canvas.setMatrix(matrix)` 和 `Canvas.concat(matrix)`。

1. `Canvas.setMatrix(matrix)`：用 `Matrix` 直接替换 `Canvas` 当前的变换矩阵，即抛弃 `Canvas`当前的变换，改用 `Matrix` 的变换（注：根据下面评论里以及我在微信公众号中收到的反馈，不同的系统中 `setMatrix(matrix)` 的行为可能不一致，所以还是尽量用 `concat(matrix)` 吧）；
2. `Canvas.concat(matrix)`：用 `Canvas` 当前的变换矩阵和 `Matrix` 相乘，即基于 `Canvas` 当前的变换，叠加上 `Matrix` 中的变换

#### 2.2.2 使用 Matrix 来做自定义变换

##### 2.2.2.1 Matrix.setPolyToPoly(float[] src, int srcIndex, float[] dst, int dstIndex, int pointCount) 用点对点映射的方式设置变换

### 2.3 使用 Camera 来做三维变换

#### 2.3.1 Camera.rotate*() 三维旋转

`Camera.rotate*()` 一共有四个方法： `rotateX(deg)` `rotateY(deg)` `rotateZ(deg)``rotate(x, y, z)`

* 下面三个例子：

* 练习一：

  ```
     canvas.save();
          camera.save();
          //cancvas移动是画布移动，所以方向相反
          camera.rotateX(30);
          canvas.translate(200 + width / 2, 200 + height / 2);
          camera.applyToCanvas(canvas);
          canvas.translate(-200 - width / 2, -200 - width / 2);
          camera.restore();
          canvas.drawBitmap(bitmap, point1.x, point1.y, paint);
          canvas.restore();


          canvas.save();
          camera.save();
          camera.rotateY(30);
          canvas.translate(600 + width / 2, 200 + height / 2);
          camera.applyToCanvas(canvas);
          canvas.translate(-600 - width / 2, -200 - height / 2);
          camera.restore();
          canvas.drawBitmap(bitmap, point2.x, point2.y, paint);
          canvas.restore();
  ```

  * 练习二：图片旋转360度

    ```
    int bitmapWidth = bitmap.getWidth();
            int bitmapHeight = bitmap.getHeight();
            int centerX = point.x + bitmapWidth / 2;
            int centerY = point.y + bitmapHeight / 2;

            camera.save();
            matrix.reset();
            camera.rotateX(degree);
            camera.getMatrix(matrix);//获取转换之后的matrix对象
            camera.restore();
            matrix.preTranslate(-centerX, -centerY);
            matrix.postTranslate(centerX, centerY);
            canvas.save();
            canvas.concat(matrix);
            canvas.drawBitmap(bitmap, point.x, point.y, paint);
            canvas.restore();
    ```

    ​

* Flipboard特效

  ```Java
  public class Practice14FlipboardView extends View {
      Paint paint = new Paint(Paint.ANTI_ALIAS_FLAG);
      Bitmap bitmap;
      Camera camera = new Camera();
      int degree;
      ObjectAnimator animator = ObjectAnimator.ofInt(this, "degree", 0, 180);

      public Practice14FlipboardView(Context context) {
          super(context);
      }

      public Practice14FlipboardView(Context context, @Nullable AttributeSet attrs) {
          super(context, attrs);
      }

      public Practice14FlipboardView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
          super(context, attrs, defStyleAttr);
      }

      {
          bitmap = BitmapFactory.decodeResource(getResources(), R.drawable.maps);

          animator.setDuration(2500);
          animator.setInterpolator(new LinearInterpolator());
          animator.setRepeatCount(ValueAnimator.INFINITE);
          animator.setRepeatMode(ValueAnimator.REVERSE);//来回执行
      }

      @Override
      protected void onAttachedToWindow() {
          super.onAttachedToWindow();
          animator.start();
      }

      @Override
      protected void onDetachedFromWindow() {
          super.onDetachedFromWindow();
          animator.end();
      }

      //忽略此警告,此处的set在动画执行的时候，动态的给degree赋值
      @SuppressWarnings("unused")
      public void setDegree(int degree2) {
          this.degree = degree2;
          Log.d("TEST", ":" + this.degree);
          Log.d("TEST1", ":" + degree2);
          Log.d("TEST2", ":" + degree);


          invalidate();
      }

      @Override
      protected void onDraw(Canvas canvas) {
          super.onDraw(canvas);

          int bitmapWidth = bitmap.getWidth();
          int bitmapHeight = bitmap.getHeight();
          int centerX = getWidth() / 2;
          int centerY = getHeight() / 2;
          int x = centerX - bitmapWidth / 2;
          int y = centerY - bitmapHeight / 2;
          //裁剪绘制上半部分
          canvas.save();
          canvas.clipRect(x, y, x + bitmap.getWidth(), y + bitmap.getHeight() / 2);
          canvas.drawBitmap(bitmap, x, y, paint);
          canvas.restore();

          canvas.save();
          //绘制下半部分，然后对下半部分做旋转，因此要灵活套用
          camera.save();
          camera.rotateX(degree);
          canvas.translate(centerX, centerY);
          camera.applyToCanvas(canvas);
          canvas.translate(-centerX, -centerY);
          camera.restore();
          canvas.clipRect(x, y + bitmap.getHeight() / 2, x + bitmap.getWidth(), y + bitmap.getHeight());
          canvas.drawBitmap(bitmap, x, y, paint);
          canvas.restore();
      }
  }
  ```

  ​