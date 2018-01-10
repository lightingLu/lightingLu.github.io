---
layout:     post
title:      "HenCode学习—Canvas使用"
subtitle:   "canvas,path,paint"
date:       2018-01-09
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

前一段时间，看了Hencoder但是没有时间练习，最近打算把看过的自定义view写一遍，今天总结的是第一章，canvas的使用，里面涉及到path，paint. 此处自定义View,继承于View,在ondraw方法里面做绘图操作。

## 1.Paint

-  Paint.setStyle(Style style)设置绘制模式
- Paint.setColor(int color)` 设置颜色
- `Paint.setStrokeWidth(float width)` 设置线条宽度
- `Paint.setTextSize(float textSize)` 设置文字大小
- `Paint.setAntiAlias(boolean aa)` 设置抗锯齿开关

## 2.Canvas

* Canvas 的绘制类方法： drawXXX() （关键参数：Paint）
* Canvas 的辅助类方法：范围裁切和几何变换

#### 绘制颜色

* Canvas.drawColor(@ColorInt int color) 颜色填充

*  drawColor(Color.BLACK)` 会把整个区域染成纯黑色，覆盖掉原有内容； ` 

*  drawColor(Color.parse("#88880000") 会在原有的绘制效果上加一层半透明的红色遮罩

*  drawRGB(int r, int g, int b)

*  drawARGB(int a, int r, int g, int b)

> 画图像，一般需要四个点的为上下左右的距离

* drawCircle(float centerX, float centerY, float radius, Paint paint) 画圆

* drawRect(float left, float top, float right, float bottom, Paint paint) 画矩形

* drawPoint(float x, float y, Paint paint) 画点

  >  点的大小可以通过 `paint.setStrokeWidth(width)` 来设置；点的形状可以通过 `paint.setStrokeCap(cap)` 来   设置：`ROUND` 画出来是圆形的点，`SQUARE` 或 `BUTT` 画出来是方形的点


 *     drawPoints(float[] pts, int offset, int count, Paint paint) / drawPoints(float[] pts, Paint paint) 画点（批量）

  ```Java
  float[] points = {0, 0, 50, 50, 50, 100, 100, 50, 100, 100, 150, 50, 150, 100};  
  // 绘制四个点：(50, 50) (50, 100) (100, 50) (100, 100)
  canvas.drawPoints(points, 2 /* 跳过两个数，即前两个 0 */,  
            8 /* 一共绘制 8 个数（4 个点）*/, paint);
  ```


*  drawOval(float left, float top, float right, float bottom, Paint paint) 画椭圆

*  drawLine(float startX, float startY, float stopX, float stopY, Paint paint) 画线

*  drawLines(float[] pts, int offset, int count, Paint paint) / drawLines(float[] pts, Paint paint) 画线（批量）

  ```Java
  float[] points = {20, 20, 120, 20, 70, 20, 70, 120, 20, 120, 120, 120, 150, 20, 250, 20, 150, 20, 150, 120, 250, 20, 250, 120, 150, 120, 250, 120};  
  canvas.drawLines(points, paint); 
  ```

*  drawRoundRect(float left, float top, float right, float bottom, float rx, float ry, Paint paint) 画圆角矩形

  `left`, `top`, `right`, `bottom` 是四条边的坐标，`rx` 和 `ry` 是圆角的横向半径和纵向半径。

*  drawArc(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean useCenter, Paint paint) 绘制弧形或扇形

  > `drawArc()` 是使用一个椭圆来描述弧形的。`left`, `top`, `right`, `bottom` 描述的是这个弧形所在的椭圆；`startAngle` 是弧形的起始角度（x 轴的正向，即正右的方向，是 0 度的位置；顺时针为正角度，逆时针为负角度），`sweepAngle` 是弧形划过的角度；`useCenter` 表示是否连接到圆心，如果不连接到圆心，就是弧形，如果连接到圆心，就是扇形 

## 3.path图形的绘制

> `Path` 可以描述直线、二次曲线、三次曲线、圆、椭圆、弧形、矩形、圆角矩形。把这些图形结合起来，就可以描述出很多复杂的图形.`Path` 有两类方法，一类是直接描述路径的，另一类是辅助的设置或计算。

### Path 方法第一类：直接描述路径

* ##### 第一组： `addXxx()` ——添加子图形

  * addCircle(float x, float y, float radius, Direction dir) 添加圆

  *  addOval(float left, float top, float right, float bottom, Direction dir) / addOval(RectF oval, Direction dir) 添加椭圆

  *  addRect(float left, float top, float right, float bottom, Direction dir) / addRect(RectF rect, Direction dir) 添加矩形

  *  addRoundRect(RectF rect, float rx, float ry, Direction dir) / addRoundRect(float left, float top, float right, float bottom, float rx, float ry, Direction dir) / addRoundRect(RectF rect, float[] radii, Direction dir)

  *  addRoundRect(float left, float top, float right, float bottom, float[] radii, Direction dir) 添加圆角矩形

  *  addPath(Path path) 添加另一个 Path

* ##### 第二组：`xxxTo()` ——画线（直线或曲线）

  * lineTo(float x, float y) / rLineTo(float x, float y) 画直线

  * quadTo(float x1, float y1, float x2, float y2) / rQuadTo(float dx1, float dy1, float dx2, float dy2) 画二次贝塞尔曲线

  * cubicTo(float x1, float y1, float x2, float y2, float x3, float y3) / rCubicTo(float x1, float y1, float x2, float y2, float x3, float y3) 画三次贝塞尔曲线

  * moveTo(float x, float y) / rMoveTo(float x, float y) 移动到目标位置

  *  arcTo(RectF oval, float startAngle, float sweepAngle, boolean forceMoveTo) / arcTo(float left, float top, float right, float bottom, float startAngle, float sweepAngle, boolean forceMoveTo) / arcTo(RectF oval, float startAngle, float sweepAngle) 画弧形

    > `forceMoveTo` 参数的意思是，绘制是要「抬一下笔移动过去」，还是「直接拖着笔过去」，区别在于是否留下移动的痕迹

  * addArc(float left, float top, float right, float bottom, float startAngle, float sweepAngle) / addArc(RectF oval, float startAngle, float sweepAngle)

      > 又是一个弧形的方法。一个叫 `arcTo` ，一个叫 `addArc()`，都是弧形，区别在哪里？其实很简单： `addArc()` 只是一个直接使用了 `forceMoveTo = true` 的简化版 `arcTo()` 。

* close()

  #### Path 方法第二类：辅助的设置或计算

  >  `WINDING` 是「全填充」，而 `EVEN_ODD` 是「交叉填充」,详情参考hencoder

  ## 4.其他方法

  *  drawText(String text, float x, float y, Paint paint) 绘制文字

  * drawBitmap(Bitmap bitmap, float left, float top, Paint paint) 画 Bitmap

  ## 5.两个Demo

  * 直方图

  ```Java

  public class Practice10HistogramView extends View {

      public Practice10HistogramView(Context context) {
          super(context);
      }

      public Practice10HistogramView(Context context, @Nullable AttributeSet attrs) {
          super(context, attrs);
      }

      public Practice10HistogramView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
          super(context, attrs, defStyleAttr);
      }

      @Override
      protected void onDraw(Canvas canvas) {
          super.onDraw(canvas);
          Paint paint=new Paint();
          paint.setStyle(Paint.Style.STROKE);
          paint.setColor(Color.BLACK);
          Path path=new Path();
          path.moveTo(100,100);
          path.lineTo(100,600);
          path.lineTo(600,600);
          canvas.drawPath(path,paint);
          paint.setColor(Color.BLUE);
          paint.setStyle(Paint.Style.FILL);
          canvas.drawRect(150,500,200,600,paint);
          canvas.drawRect(250,300,300,600,paint);
          canvas.drawRect(350,100,400,600,paint);
      }
  }

  ```

  * 饼图

  ```Java

  public class Practice11PieChartView extends View {

      public Practice11PieChartView(Context context) {
          super(context);
      }

      public Practice11PieChartView(Context context, @Nullable AttributeSet attrs) {
          super(context, attrs);
      }

      public Practice11PieChartView(Context context, @Nullable AttributeSet attrs, int defStyleAttr) {
          super(context, attrs, defStyleAttr);
      }

      @RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
      @Override
      protected void onDraw(Canvas canvas) {
          super.onDraw(canvas);

  //        canvas.drawLine();

          Paint paint=new Paint();
          paint.setStyle(Paint.Style.FILL);
          paint.setColor(Color.RED);
          canvas.drawArc(200,50,600,450,180,100,true,paint);

          paint.setColor(Color.BLUE);
          canvas.drawArc(220,50,600,470,280,80,true,paint);

          paint.setColor(Color.GREEN);

          canvas.drawArc(200,70,600,450,80,100,true,paint);
          paint.setColor(Color.YELLOW);

          canvas.drawArc(205, 70, 600, 450, 0, 70, true, paint);
      }
  }

  ```

  ​