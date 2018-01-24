---
layout:     post
title:      “保持屏幕亮屏的几种方法"
subtitle:   "合理控制亮屏时间"
date:       2018-01-24
author:     "light"
header-img: ""
catalog:    true
tags:
    - android

---

### 1 .使用newWakeLock方法
```java
 this.powerManager = (PowerManager) this
                .getSystemService(Context.POWER_SERVICE);
        this.wakeLock = this.powerManager.newWakeLock(
                PowerManager.FULL_WAKE_LOCK, "My Lock");
        //保持常亮
        this.wakeLock.acquire();
      //释放
        this.wakeLock.release();
```
### 2 .使用 FLAG_KEEP_SCREEN_ON设置

```java
public class MainActivity extends Activity{

@Override
protectedvoid onCreate(Bundle savedInstanceState){

super.onCreate(savedInstanceState);
setContentView(R.layout.activity_main);

getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);

}
}


```


> 这个方法的好处是，与wakelocks不同 ,它不需要具体的权限，并且在换不同应用程序的操作中，系统会管理，不必担心没有释放的未使用的资源。你并不需要清除FLAG_KEEP_SCREEN_ON标志，除非你不再需要在屏幕上停留在你运行应用程序。当应用程序进入后台或返回到前台发生，窗口管理器负责保障正常的事件处理，但是如果你明确的想要清除这个标致，从而允许屏幕熄灭，可以使用clearFlags()。
如下：

```language
getWindow().clearFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON)
```
### 3.在XML文件中，使用android:keepScreenOn属性

```java
<relativelayoutxmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="match_parent"
android:keepScreenOn="true">
  ...
</relativelayout>
```

这个设置android:keepScreenOn="true"等同于FLAG_KEEP_SCREEN_ON。通过设置控件的这个属性，可以控件亮屏和灭屏。

  ​