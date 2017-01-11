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













1. 数据库使用greenDao
2. 2439 leak
3. 加壳,脱壳
4. app 数据安全


5. ViewPager outOfBound
6. ...


7. android 7.0
8. 购买页面, 利率收益相关重构(暂时不用mvp)
9. RxJava2.0, RxAndroid 2.0