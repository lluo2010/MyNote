
# Android 6.0运行时权限


## 原来权限模型

---

　在Android 6.0版本之前，权限都是一条龙服务的，只要用户安装完，AndroidManifest清单上申请的权限都会被系统默认授权，并且授权后也撤销不了。这样的弊端在哪里呢？有些权限可能用户觉得不需要，比如他不想有通知的权限，不想受到通知的干扰，那么他就不能屏蔽通知，就是不需要的权限，他去不掉，自主权不在他那边。还有一些情况是，一些恶意程序，会利用这个权限默认授权，进行恶意获取用户数据和攻击。所以Android 6.0版本，一方面让用户更加容易的控制自己的隐私，一方面需要重新适配应用权限。

## Android 6.0权限模型

---

  采用新的权限模型，只有在需要权限的时候，才告知用户是否授权，是在runtime时候授权，而不是在原来安装的时候 ，同时默认情况下每次在运行时打开页面时候，需要先检查是否有所需要的权限申请。这样的用户的自主性提高很多，比如用户可以给APP赋予摄像的权限，但是可以拒绝记录设备位置的权限，就是怕位置信息上传等等。


## 权限流程

---

在API 23中，权限满足的标准流程：

![流程图](http://images2015.cnblogs.com/blog/331079/201602/331079-20160204114953382-1387981895.png)

但这里有个问题，那就是在系统授权弹窗环节，提醒框会有个不再提示的复选框，如果用户点击不太提示，并拒绝授权，那么再下次授权的时候，系统授权弹窗的提示框就不会在提示，所以我们很有必要需要自定义权限弹窗提示框，那么流程图就变成如下了:

![流程图](http://images2015.cnblogs.com/blog/331079/201602/331079-20160204115015975-469422810.png)



## 权限类型

---

系统权限中，又分为normal和dangerous类型:

1. normal: 这个权限类型并不直接威胁到用户的隐私，可以直接在manifest清单里注册，系统会帮我们默认授权的。
1. dangerous:这个可以直接给app访问用户一些敏感的数据，不仅需要在manifest清单里注册，同时在使用的时候，需要向系统请求授权。


## 危险权限和权限组。

---

权限组|权限
-|-
CALENDAR| READ_CALENDAR, WRITE_CALENDAR
CAMERA	| CAMERA
CONTACTS| READ_CONTACTS, WRITE_CONTACTS, GET_ACCOUNTS
LOCATION| ACCESS_FINE_LOCATION, ACCESS_COARSE_LOCATION
MICROPHONE| RECORD_AUDIO
PHONE| READ_PHONE_STATE, CALL_PHONE, READ_CALL_LOG, WRITE_CALL_LOG, ADD_VOICEMAIL, USE_SIP, PROCESS_OUTGOING_CALLS
SENSORS	| BODY_SENSORS
SMS	| SEND_SMS, RECEIVE_SMS, READ_SMS, RECEIVE_WAP_PUSH, RECEIVE_MMS
STORAGE	| READ_EXTERNAL_STORAGE, WRITE_EXTERNAL_STORAGE


## 总结

---

1. 首先要知道6.0版本权限模型跟原来版本是不同的，不再是统一在manifest中默认系统授权，而是有需要的时候，向系统请求授权，提高用户体验。
1. 了解权限检测流程，一点注意点是如果系统权限弹窗提示框被不再提醒了，需要我们自定义提示弹窗，引导用户去授权。
1. 明白权限类型，分为normal和dangerous类型，同时，在dangerous中还需要注意一点，SYSTEM_ALERT_WINDOW 和 WRITE_SETTINGS这两个特殊授权请求方式，跟一般授权请求方式不同。
1. 在判断APP是否运行在Android M上，可以用版本号来判断，可以准确点。


## 相关链接
1. [谈谈Android 6.0运行时权限理解](http://www.cnblogs.com/cr330326/p/5181283.html)
1. [在运行时请求权限](https://developer.android.com/training/permissions/requesting.html)
1. [android 6.0 权限管理的学习资料和使用例子](http://blog.csdn.net/yangqingqo/article/details/48371123)
1. [系统权限](https://developer.android.com/guide/topics/security/permissions.html#defining)
1. [聊一聊Android 6.0的运行时权限**](http://droidyue.com/blog/2016/01/17/understanding-marshmallow-runtime-permission/)