---
title: 利用Bomb打造自己的小程序
tags: []
id: '51'
categories:
  - - 小程序
date: 2018-06-15 19:36:00
---

*   [小程序开发](https://developers.weixin.qq.com/miniprogram/dev/component/ "小程序开发")
*   [Bomb免费后端云开发](http://doc.bmob.cn/ "Bmob免费后端云开发")

首先,小程序的开发已是热门,一个前段技术人员必备的技术就是开发小程序.在这里推荐一个[入门小程序文章(连胜出品)](https://mp.weixin.qq.com/s/wGxtT2w2_9gJcjHGFsguuw)。

对于小程序的入门开发就不再做详细介绍,这里针对Bmob辅助开发小程序进行介绍。

　　1\. Bmob是什么?

　　Bmob 为小程序开发提供一体化的后端云服务，免去了服务器维护、证书配置、数据存储互通等繁琐的工作，大幅度提高开发效率，减少研发时间和成本。接入 Bmob，还能同时打通小程序、App、Web 之间的数据，提高运营效率。

　　利用Bomb可以做到WEB，APP，小程序共享一套数据，并且是免费的提供20G的存储空间，对于个人开发足以。

　　2\. Bmob开发

　　->注册Bmob

　　->创建一个应用

　　![](/images/905bbb9392.png)

　　接着将自己注册的小程序对应的AppId以及AppSecert填入Bmob,如下图:

　　![](/images/21504491a3.png)

　　之后,在应用秘钥中获取Bomb提供的Application ID和REST API Key,这是进入Bmob的凭证,注意保护...

　　![](/images/8105714cfe.png)

　　此时,下载Bmob SDK(Bomb.min.js),将此SDK放入utils文件下,这样就可以在app.js中进行引入

var Bmob = require('../../utils/bmob.js');
Bmob.initialize("你的Application ID", "你的REST API Key");

　　此时,可以进行对数据的查询:

var Diary = Bmob.Object.extend("diary");  
  
//利用每一条对应的ObjectId查询 var query = new Bmob.Query(Diary);
 　　query.get("4edc3f6ee9", {
      success: function(result) { // The object was retrieved successfully.
        console.log("该日记标题为"+result.get("title"));
      },
      error: function(result, error) {
        console.log("查询失败");
      }
    });  
  
//利用其它字段进行查询  
query.equalTo("title", "bmob");//利用title字段(示例)  
query.find({   
　　success: function(results) {   
　　　　console.log("共查询到 " + results.length + " 条记录"); // 循环处理查询到的数据   
　　　　for (var i = 0; i < results.length; i++) {   
　　　　　　var object = results\[i\]; console.log(object.id + ' - ' + object.get('title')); }   
　　},   
　　error: function(error) {   
　　　　console.log("查询失败: " + error.code + " " + error.message);   
　　}   
});  

　　除了查询,还可以进行对应的增删改,eg:

//增加数据   创建类和实例
    var Diary = Bmob.Object.extend("diary"); var diary = new Diary();
    diary.set("title","hello");
    diary.set("content","hello world"); //添加数据，第一个入口参数是null
    diary.save(null, {
        success: function(result) { // 添加成功，返回成功之后的objectId（注意：返回的属性名字是id，不是objectId），你还可以在Bmob的Web管理后台看到对应的数据
            console.log("日记创建成功, objectId:"+result.id);
        },
        error: function(result, error) { // 添加失败
          console.log('创建日记失败');

        }
    });
  
//删除数据 //单个请求每次最多删除50条。
 var query = new Bmob.Query('diary');
 query.equalTo("title", "后端云");
          query.find().then(function (todos) { return Bmob.Object.destroyAll(todos);
          }).then(function (todos) {
            console.log(todos); // 删除成功
          }, function (error) { // 异常处理
 }); // 更改数据
var Diary = Bmob.Object.extend("diary"); var Diary = new Diary();
Diary.set("title", "111");
Diary.set("content", "222"); //添加数据
Diary.save(null, {
  success: function(result) { // 修改数据
    result.set("title", "111");
    result.set("content", "222");
    result.save();
  }
})

　　当然,Bmob服务不至于数据服务,只是更多的我们操作数据多一点.

　　例如:短信验证码服务:

Bmob.Sms.requestSmsCode({"mobilePhoneNumber": "131xxxxxxxx"} ).then(function(obj) {
  console.log("smsId:"+obj.smsId); // }, function(err){
  console.log("发送失败:"+err);
});

返回数据:
{ "smsId": 1232222 }

　　还有更多好玩的,不再做一一展示,Bmob官方文档比较浅显易懂..

![](/images/ce64fb5e6d.png)