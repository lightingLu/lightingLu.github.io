---
layout:     post
title:      "HenCode学习—Paint使用"
subtitle:   "aint"
date:       2018-01-16
author:     "light"
header-img: ""
catalog:    true
tags:
    - 学习
    - Hencoder
    - 自定义控件
    - 绘图

---

# HenCoder 学习—drawText常用的方法

 今天总结的是第三章，主要讲drawText相关的一些方法。

## 1 Canvas 绘制文字的方式

### 1.1 drawText(String text, float x, float y, Paint paint)

### 1.2 drawTextRun()

### 1.3 drawTextOnPath()

### 1.4 StaticLayout

如果需要绘制多行的文字，你必须自行把文字切断后分多次使用 `drawText()` 来绘制，或者——使用 `StaticLayout` 。`StaticLayout` 的构造方法是 StaticLayout(CharSequence source, TextPaint paint, int width, Layout.Alignment align, float spacingmult, float spacingadd, boolean includepad)，其中参数里：

`width` 是文字区域的宽度，文字到达这个宽度后就会自动换行； 
`align` 是文字的对齐方向； 
`spacingmult` 是行间距的倍数，通常情况下填 1 就好； 
`spacingadd` 是行间距的额外增加值，通常情况下填 0 就好； 
`includeadd` 是指是否在文字上下添加额外的空间，来避免某些过高的字符的绘制出现越界。

## 2 Paint 对文字绘制的辅助

#### 2.1.1 setTextSize(float textSize)

#### 2.1.2 setTypeface(Typeface typeface) 甚至字体

```Java
paint.setTypeface(Typeface.DEFAULT);  
canvas.drawText(text, 100, 150, paint);  
paint.setTypeface(Typeface.SERIF);  
canvas.drawText(text, 100, 300, paint);  
//获取字体
paint.setTypeface(Typeface.createFromAsset(getContext().getAssets(), "Satisfy-Regular.ttf"));  
canvas.drawText(text, 100, 450, paint);  
```

#### 2.1.3 setFakeBoldText(boolean fakeBoldText) 设置粗体

#### 2.1.4 setStrikeThruText(boolean strikeThruText) 删除线

#### 2.1.5 setUnderlineText(boolean underlineText) 下划线

#### 2.1.6 setTextSkewX(float skewX) 设置文字横向错切角度。其实就是文字倾斜度的啦

#### 2.1.7 setTextScaleX(float scaleX) 设置文字横向放缩。也就是文字变胖变瘦

#### 2.1.8 setLetterSpacing(float letterSpacing)  设置字符间距。默认值是 0，负值紧凑

#### 2.1.9 setFontFeatureSettings(String settings) 用 CSS 的 `font-feature-settings` 的方式来设置文字

#### 2.1.10 setTextAlign(Paint.Align align)

设置文字的对齐方式。一共有三个值：`LEFT` `CETNER` 和 `RIGHT`。默认值为 `LEFT`。

#### 2.1.11 setTextLocale(Locale locale) / setTextLocales(LocaleList locales)

设置绘制所使用的 `Locale`。其实就是你在系统里设置的「语言」

#### 2.1.12 setHinting(int mode) 设置是否启用字体的 hinting （字体微调）



### 2.2 测量文字尺寸类

#### 2.2.1 float getFontSpacing()

获取推荐的行距。

#### 2.2.2 FontMetircs getFontMetrics() 排版用

#### 2.2.3 getTextBounds(String text, int start, int end, Rect bounds)

获取文字的显示范围。

#### 2.2.4 float measureText(String text)

测量文字的宽度并返回