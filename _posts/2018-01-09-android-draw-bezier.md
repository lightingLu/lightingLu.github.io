---
layout:     post
title:      "Android开发 - 图形化生成的贝塞尔插值器【转】"
subtitle:   "基于三次方贝塞尔曲线的插值器"
date:       2018-01-09
author:     "keysaim"
header-img: ""
catalog:    true
tags:
    - android
    - 动画

---

# 基于三次方贝塞尔曲线的插值器

在动画开发过程中，经常需要使用到插值器来满足我们的动画设计需求。然而，官方提供的插值器并不能满足所有的需求，所以我们需要自定义插值器。
下面介绍的三次方贝塞尔曲线的插值器，主要可用于实现连续曲线的动画。
关于贝塞尔曲线的介绍，请移步[百度](https://www.baidu.com/)。

## 绘制满足设计的动画曲线
#### 1.先使用贝塞尔曲线数值生成工具来获取想要的曲线数值
工具网站：[cubic-bezier.com](http://cubic-bezier.com/#.17,.67,.83,.67)

使用教程：
* 1.拉拽左边图像的2个点，调整出符合效果的图形
* 2.点击右上角的Save按钮，将4个参数运用到下面的代码中。
[](https://images0.cnblogs.com/blog2015/669482/201503/301038387643060.png)
[](https://images0.cnblogs.com/blog2015/669482/201503/301039079825352.png)


#### 2.代码运用
```
 new EaseCubicInterpolator(0.31f, 0.85f,0.77f, 0.14f);

```
如上直接调用下面的差值器类，构造方法中的4个参数，即是从第一步的网站获得的数值。
```
/**

 * 缓动三次方曲线插值器.(基于三次方贝塞尔曲线)

 */

public class EaseCubicInterpolator implements Interpolator {



    private final static int ACCURACY = 4096;

    private int mLastI = 0;

    private final PointF mControlPoint1 = new PointF();

    private final PointF mControlPoint2 = new PointF();



    /**

     * 设置中间两个控制点.<br>

     * 在线工具: http://cubic-bezier.com/<br>

     * 

     * @param x1

     * @param y1

     * @param x2

     * @param y2

     */

    public EaseCubicInterpolator(float x1, float y1, float x2, float y2) {

        mControlPoint1.x = x1;

        mControlPoint1.y = y1;

        mControlPoint2.x = x2;

        mControlPoint2.y = y2;

    }



    @Override

    public float getInterpolation(float input) {

        float t = input;

        // 近似求解t的值[0,1]

        for (int i = mLastI; i < ACCURACY; i++) {

            t = 1.0f * i / ACCURACY;

            double x = cubicCurves(t, 0, mControlPoint1.x, mControlPoint2.x, 1);

            if (x >= input) {

                mLastI = i;

                break;

            }

        }

        double value = cubicCurves(t, 0, mControlPoint1.y, mControlPoint2.y, 1);

        if (value > 0.999d) {

            value = 1;

            mLastI = 0;

        }

        return (float) value;

    }



    /**

     * 求三次贝塞尔曲线(四个控制点)一个点某个维度的值.<br>

     * 参考资料: <em> http://devmag.org.za/2011/04/05/bzier-curves-a-tutorial/ </em>

     * 

     * @param t

     *            取值[0, 1]

     * @param value0

     * @param value1

     * @param value2

     * @param value3

     * @return

     */

    public static double cubicCurves(double t, double value0, double value1,

            double value2, double value3) {

        double value;

        double u = 1 - t;

        double tt = t * t;

        double uu = u * u;

        double uuu = uu * u;

        double ttt = tt * t;



        value = uuu * value0;

        value += 3 * uu * t * value1;

        value += 3 * u * tt * value2;

        value += ttt * value3;

        return value;

    }

}
```


