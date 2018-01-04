---
layout:     post
title:      "Android Webview重定向问题解决"
subtitle:   "webview"
date:       2018-01-14
author:     "light"
header-img: ""
catalog:    true
tags:
    - android
    - webview
---

   项目中需要webview重定向，但是由于一个webveiw里面有许多加载操作，因此在调用webview。goback()方法时，往往达不到我们需要的操作效果。

####1.解决方法
**WebBackForwardList webBackForwardList=webview.copyBackForwardList()获取webview加载栈，然后更具加载栈做逻辑操作**
####2.webBackForwardList常用的方法
* int size = webBackForwardList.getSize()
* webBackForwardList.getCurrentItem()
* webBackForwardList.getCurrentIndex()
* webBackForwardList.getItemAtIndex(index)

getsize()方法获取当前加载栈的长度；
getCurrentItem()获取当前webview所加载的界面，我们可以在这个方法下获得url,title等内容；
getCurrentIndex()获取当前加载在加载栈中的位置；
webBackForwardList.getItemAtIndex(index)获取加载栈中第index页面；
####3.合理使用
在2中我们获取到加载页面的一些信息，然后可以根据当前url,标题，位置。来进行相应的处理。
####4.Demo使用
现在我们有这样一个场景：打开一个activity,webview加载A界面,然后用户在A里面点击B，然后点击C，点击D。返回操作时我们需要从D跳到B，然后跳A.下面为代码：

```
  public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (webView.canGoBack()) {
            WebBackForwardList webBackForwardList = webView.copyBackForwardList();
            if (webBackForwardList.getCurrentIndex() == 4) {//当前处于D界面
                webView.loadUrl(webBackForwardList.getItemAtIndex(1).getUrl());//挑转到B界面
                return true;
            }
            webView.goBack();
        }
       
        return true;
    }

```




[Linux_Doc_Proj]: http://www.tldp.org/index.html
[LPIC]: https://en.wikipedia.org/wiki/Linux_Professional_Institute_Certification
