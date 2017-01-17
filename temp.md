url replace:

1. mUrl = BasicUtil.buildCompleteUrl(url, Constants.IMG_BASE);
一般的图片:
https://www.stlc.cn/Uploads/msgicon/+xxxx

1. 
"https://www.stlc.cn/Uploads/focus/" + xxxxx

- 推荐页banner上的图片.
    return BasicUtil.buildCompleteUrl(image, Constants.IMG_FOCUS);
- 理财日历上banner图片
    BasicUtil.buildCompleteUrl(advertisementsBean.image, Constants.IMG_FOCUS);
- 弹窗上图片
    ImageLoader.getInstance().displayImage(BasicUtil.buildCompleteUrl(bean.image, Constants.IMG_FOCUS),ivpromotion,build);
- 启动页上的图片
    String url = BasicUtil.buildCompleteUrl(result.image, Constants.IMG_FOCUS);




1. 下面几种图片,采用"https://www.stlc.cn/Uploads/focus/"拼接:

    - 推荐页banner上的图片.
    - 理财日历上banner图片
    - 弹窗上图片
    - 启动页上的图片

2. 其他没有http(s)开头的图片使用 https://www.stlc.cn/Uploads/msgicon/拼接.










# Javascript模块化





## Reference

---

1. [JavaScript 模块化历程](http://web.jobbole.com/83761/)









Dialog详解（包括进度条、PopupWindow、自定义view、自定义样式的对话框）
http://www.cnblogs.com/tianzhijiexian/p/3867731.html




1. Dialogs
     http://developer.android.com/guide/topics/ui/dialogs.html
2. Dialog详解（包括进度条、PopupWindow、自定义view、自定义样式的对话框）
    http://www.cnblogs.com/tianzhijiexian/p/3867731.html




FragmentPagerAdapter






关于RxJava最友好的文章——背压
https://zhuanlan.zhihu.com/p/24473022

关于RxJava最友好的文章（初级篇）
https://zhuanlan.zhihu.com/p/23584382

关于RxJava最友好的文章（进阶篇）
https://zhuanlan.zhihu.com/p/23585300



direct buffer


```k`
todo list:
Annotation