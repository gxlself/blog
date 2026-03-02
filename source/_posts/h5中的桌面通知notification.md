---
title: H5中的桌面通知Notification
tags: []
id: '141'
categories:
  - - Javascript
date: 2018-09-24 15:10:02
---

## H5中的桌面通知Notification

#### 　　前言:?对于一个前段开发者,逛网页总会留意一些新奇的功能,对于上班总会用到Teambition的我,总是能收到Notification...所以今天就来研究下这个H5功能...

* * *

  1. 实例一个Notification

![复制代码](/images/48304ba5e6.gif)

let n = new Notification(
　　"这是一个通知消息",                  //这是必选Title  一定会显示的通知标题
　　{
  　　 icon: "xxx.png",               // 这个icon是用来显示通知中的左边图片
  　　 body: "你好啊,我是xxx",      　　// 通知中的内容字符
　　　 dir: auto,　　　　　　　　　　　　　// 文字的方向 值包含: auto(自动), ltr(从左到右), rtl(从右向左)
　　　 tag: "gxlself"　　　　　　　　　　//  给与这个通知消息一个ID, 用来对这个通知消息进行 刷新.移除.替换 等操作
　　　　// long: "en-US"　　　　　　　　　　　　　　　//lang字段 需要参考https://tools.ietf.org/html/bcp47 并不是必须 没看出啥作用...
　　　　// ...　　　　　　　　　　　　// 其余可选属性见下实例属性
　　}
)
访问对应的实例属性:
n.actions // 一个只读的NotificationAction对象数组。每一个对象描述用户可以在一个通知中选择的单个操作。
n.image   // 通知的一部分显示的图像的URL
n.badge   // 当没有足够的空间显示通知本身时，用于表示通知的图像的URL。
n.permission   // 有三个值 `granted`, `denied`, 或`default  当状态值为granted时可以发送通知消息  default默认用户没处理  denied 用户拒绝  ` n.renotify // 布尔值。新通知出现的时候是否替换之前的。如果设为`true`，则表示替换，表示当前标记的通知只会出现一个。注意这里的“当前标记”没？没错，`true`参数要想起作用，`tag必须`需要设置属性值。 n.requireInteraction // 布尔值, 指的是通知是否保持活动直到用户点击或取消通知 而不是自动关闭.. n.silent // 布尔值。通知出现的时候，是否要有声音。默认`false`, 表示无声。 n.timestamp //通知创建或者可以使用的时间。 n.data // 任意类型和通知相关联的数据。 n.vibrate // 通知显示时候，设备震动硬件需要的震动模式。所谓振动模式，指的是一个描述交替时间的数组，分别表示振动和不振动的毫秒数，一直交替下去。例如\[200, 100, 200\]表示设备振动200毫秒，然后停止100毫秒，再振动200毫秒。 (移动端) n.sound // 字符串。音频地址。表示通知出现要播放的声音资源。 n.sticky // 通知吸附不容易被清除... (移动端) n.noscreen // 布尔值。是否不再屏幕上显示通知信息。默认`false`, 表示要在屏幕上显示通知内容。(移动端) n. 此时实例 n 有四个事件处理: 1> onclick 用户对通知信息的点击事件 2> onshow 通知消息展示之后触发的事件 3> onerror 遇到错误会触发的事件 4> onclose close事件的处理

![复制代码](/images/48304ba5e6.gif)

2\. Notification对象会有什么属性/方法呢?利用控制台中的window对象输出点开查看就可以看到: ![](/images/4179227bd1.jpg) 值得注意的是: requestPermission()方法仅在Notification对象有效,实例对象无效!!!这个方法是用来向用户申请显示通知的权限,只能被用户主动去调用(在页面onload中可以调用,可以向用户申请,之后再去发送...)   实例对象拥有的方法有: (1). close()? 用于关闭通知消息 --> 也可以在onshow方法加延迟调用,提高用户体验感... (2). addEventListener() 监听事件(这个通用方法) (3). removeEventListener 卸载监听事件(通用,同上) (4). dispatchEvent 分派事件(同上)   接下来,写一个js测试, 如果使用的是谷歌浏览器,建议在设置中显示通知将本地服务地址加入允许通知 但是,http的域名在谷歌浏览器被默认关闭,还不允许更改,查看谷歌浏览器控制台有警告信息---> index.js:78 \[Deprecation\] The Notification API may no longer be used from insecure origins. You should consider switching your application to a secure origin, such as HTTPS. See https://goo.gl/rStTGz for more details. 嗯,好吧,让加https证书,真的是有毒...虽然在自己的主页中添加该功能也只能在火狐浏览器爽一爽.... (腾讯云有免费一年的ssl证书,可自己进行安装...)

![复制代码](/images/48304ba5e6.gif)

// index.js
window.onload = function(){
　　let gxlself = new Gxlself()
　　gxlself.requestPermission()
　　setTimeout(()=>{
　　　　gxlself.showNotification()
　　},3000)
}

class Gxlself{
　　 constructor(){
　　　　this.isNotificationSupported = "Notification" in window;
　　 }
    isPermissionGranted(){
        return Notification.permission === 'granted';
    }
    requestPermission(){
        if(!this.isNotificationSupported){
            return;
        }
        Notification.requestPermission(status=>{
            let permission = Notification.permission;
        })
    }
    showNotification(){
        if (!this.isNotificationSupported) {
            return;
        }
        if (!this.isPermissionGranted()) {
            return;
        }
        var n = new Notification("gxlself对您发来问候", {
            icon : 'gxlself.png',
            body : '欢迎来访,鄙人万分感激! 点击即可跳转至我的博客页面~'
        });
        n.onshow = function () {
            console.log('gxlself已经发送通知信息');
            setTimeout(function() {
                n.close();
            }, 5000);
        }
        n.onclick = function () {
            location.href = 'http://gxlself.com/blog'
            n.close()
        }
        n.onerror = function (err) {
            console.log(err)
        }
        n.onclose = function () {
            console.log('gxlself消息窗口关闭')
        }
    }  
}

![复制代码](/images/48304ba5e6.gif)

这是火狐浏览器执行后的效果展示: ![](/images/6e034260c3.png)   再来看看谷歌本地跑的效果:(域名已经被默认拦截关闭,上面已经说明,不再重复) ![](/images/3a68de8a09.png) 谷歌的效果其实挺好看的,嗯...只是一个https把我给屏蔽了? ?哎... -------? 桌面版应用 ----------- 当你要在开放 web 应用中使用通知时，请确保将?`desktop-notification`?权限添加到你的 manifest 文件中。通知可以被用于任何权限级别，hosted 或更高。

```
　　"permissions": {
  　　  "desktop-notification":{}
　　}
```

* * *

  这个Notification比较好玩,也是未来发送消息的一个重要的一块,留此记录...