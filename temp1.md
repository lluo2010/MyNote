-23 15:48:50,520  INFO http-nio-8080-exec-1 (com.stone.interceptor.AuthFilter:86) - request url : /stone-rest/rest/project/investV2.json, params : {deviceType=2, amount=100, interestCoupon=, phoneSystemVersion=5.1.1, bankCardNo=6214242710498301, rechargeNo=, bankType=CCB, supperUserId=, mobile=13220482188, channel=default_channel, payWay=0, versionName=1.2.0, cardNo=210302196001012114, versionCode=75, token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=, realName=韩梅梅, requestid=, registrationId=, userIp=192.168.202.244, deviceSerialId=eb68672e7cbb8f11, validatecode=, id=1}
2017-03-23 15:48:50,522 ERROR http-nio-8080-exec-1 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:50,524  INFO http-nio-8080-exec-1 (org.apache.cxf.interceptor.LoggingInInterceptor:250) - Inbound Message
----------------------------
ID: 17
Address: http://139.196.41.47:8080/stone-rest/rest/project/investV2.json
Encoding: UTF-8
Http-Method: POST
Content-Type: application/x-www-form-urlencoded
Headers: {connection=[Keep-Alive], Content-Length=[425], content-type=[application/x-www-form-urlencoded], expect=[100-continue], host=[139.196.41.47:8080]}
--------------------------------------
2017-03-23 15:48:50,525  INFO http-nio-8080-exec-1 (org.apache.cxf.jaxrs.utils.FormUtils:173) - bankCardNo=6214242710498301&versionCode=75&token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA%3D&mobile=13220482188&realName=%E9%9F%A9%E6%A2%85%E6%A2%85&deviceType=2&rechargeNo=&userIp=192.168.202.244&deviceSerialId=eb68672e7cbb8f11&supperUserId=&phoneSystemVersion=5.1.1&versionName=1.2.0&amount=100&bankType=CCB&id=1&requestid=&payWay=0&registrationId=&validatecode=&channel=default_channel&interestCoupon=&cardNo=210302196001012114
2017-03-23 15:48:50,526 ERROR http-nio-8080-exec-1 (com.stone.rest.service.project.impl.RestProjectServiceImpl:1625) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:50,647 ERROR http-nio-8080-exec-1 (com.stone.rest.service.project.impl.RestProjectServiceImpl:835) - amount verify : true
********************代签名字符串为begin*******/ncard_no=6214242710498301&merchant_id=100000000000147&version=3.1.3g0be2385657fa355af68b74e9913a1320af82gb7ae5f580g79bffd04a402ba8f/n********************代签名字符串end*******
********************签名sign为：402ee8acc11151923b75bcf9f240fc1b
2017-03-23 15:48:50,662  INFO http-nio-8080-exec-1 (com.reapal.utils.Decipher:61) - 融宝API 准备去加密和融宝交互的请求参数是:{"card_no":"6214242710498301","sign":"402ee8acc11151923b75bcf9f240fc1b","merchant_id":"100000000000147","version":"3.1.3","sign_type":"MD5"}
2017-03-23 15:48:50,724  INFO http-nio-8080-exec-1 (com.reapal.utils.Decipher:73) - 融宝API 密文key============>XjKVN8L1/RFtgxVDWBe384wwYXG9SZj+tBLD1ggqlhTEm+A60qLKwnyDctJ4VoxEljuhp1y1aISKpAtnG2mcyqXh+IdfcP+MaG2eiN94vtdsOITKCBEjEKuPZQHXvByZ4nOH/9pXcOgKkZeifRLwQZPUm6WmAYhxj28Dh09GtGplP2ubINGjxft/7j5P88N7ItEsX8wXLcyQYe/8DFPSeinp1p6tfzRyUbNQMJ9X3sRPT2h8IByF8YFbSA1M5iglU7yEgC9BzNj6oCIGjpCgcGn2W1owU4Ctr89Zn6DbdJ8XSc9cC0+PiLTyif8Fz5j+OR7AILOY60gcf9Bs2fHmMA==
2017-03-23 15:48:50,724  INFO http-nio-8080-exec-1 (com.reapal.utils.Decipher:74) - 融宝API ===========>3j8rpqmt+aWW56azQUghqGPgla+F6hXplqmsKXuoDq4DopGc5pq50j0co28GiGaY4VCJrXP09h97pDDqJgFpdjCn09VY0qa0/OS1c2mJ3rE8AjWDorjMBKrJDho+rwvpRuGgegU0gtsGpeayEbAxm5LopXBaLy5Dr5nQW+Au2xzvRE0dqhAcfaaL/93IyyNd
2017-03-23 15:48:51,816  INFO http-nio-8080-exec-1 (com.reapal.repalPay.ReapalPay:84) - 卡号:6214242710498301融宝验证的结果是:{"bank_card_type":"0","bank_code":"CCB","bank_name":"建设银行","card_no":"6214242710498301","merchant_id":"100000000000147","result_code":"0000","result_msg":"查询成功"}
2017-03-23 15:48:51,817  INFO http-nio-8080-exec-1 (com.stone.rest.service.project.impl.RestProjectServiceImpl:883) - real bankType is: CCB
2017-03-23 15:48:51,855  INFO http-nio-8080-exec-1 (com.stone.rest.service.project.impl.RestProjectServiceImpl:909) - real payWay is 4
2017-03-23 15:48:51,858  INFO http-nio-8080-exec-1 (com.stone.service.user.impl.UserServiceImpl:619) - 生成的定期投资订单号是:R0000020001170323970
********************代签名字符串为begin*******/nbody=body&card_no=6214242710498301&cert_no=210302196001012114&cert_type=01&currency=156&member_id=13220482188&member_ip=192.168.202.244&merchant_id=100000000000147&notify_url=http://139.196.41.47:8080/stone-rest/payment/notify/reapal/payNotify.htm&order_no=R0000020001170323970&owner=韩梅梅&phone=13220482188&seller_email=hongchuanluo@qq.com&terminal_info=eb68672e7cbb8f11&terminal_type=mobile&title=title&token_id=a46e34456c4747d0b12a218fdf9d9260&total_fee=10000&transtime=2017-03-23 15:48:51&version=3.1.3g0be2385657fa355af68b74e9913a1320af82gb7ae5f580g79bffd04a402ba8f/n********************代签名字符串end*******
********************签名sign为：003b074281121e68bd1fe65783f12987
2017-03-23 15:48:51,878  INFO http-nio-8080-exec-1 (com.reapal.utils.Decipher:61) - 融宝API 准备去加密和融宝交互的请求参数是:{"owner":"韩梅梅","order_no":"R0000020001170323970","member_id":"13220482188","terminal_type":"mobile","cert_type":"01","seller_email":"hongchuanluo@qq.com","cert_no":"210302196001012114","sign":"003b074281121e68bd1fe65783f12987","terminal_info":"eb68672e7cbb8f11","merchant_id":"100000000000147","title":"title","body":"body","notify_url":"http://139.196.41.47:8080/stone-rest/payment/notify/reapal/payNotify.htm","member_ip":"192.168.202.244","version":"3.1.3","transtime":"2017-03-23 15:48:51","card_no":"6214242710498301","token_id":"a46e34456c4747d0b12a218fdf9d9260","phone":"13220482188","total_fee":"10000","currency":"156","sign_type":"MD5"}
2017-03-23 15:48:51,880  INFO http-nio-8080-exec-1 (com.reapal.utils.Decipher:73) - 融宝API 密文key============>ubCJvtpCLlwAB+2EaxbVbmturORTac9e/fg18rwzrYBrkD5EumW3jK3EVxK/0vxlHxGzB2T/4OJrQqqGQmxZQNg9wceprFLI/tO6VePfcUHZPivGq3N3XIcbe7E49AxafcObEtRxorpIsyDAIKrIKJWLsPdUhU3tIxWuWRtI54P6nmIB6uZ9mPOUE+FDc9vgfUhfLj5qGtGgfNh4uLm/aQ15gt1T8ItdCHp0KozMUj2Oo/7xJQK5uf5KHWQKGLkQ9y0NOAEuV1mE/9K1QmSn1JkIoNTZOc3h/X9sDo89URCbk6Vm+P0HAAVRoLaAtVgoIqBcJwu5PRfL+9iIPr5DXQ==
2017-03-23 15:48:51,881  INFO http-nio-8080-exec-1 (com.reapal.utils.Decipher:74) - 融宝API ===========>ZyzpAiJywQaNbVbxL7/NYJtGqa1bbcmW/VnEPWVUaulZ7vLO5wTlUthmT8Ems+x/kOI+pcfi57EDsTzVsx0Nruy/SJknVquuGpP7uIx1+iU0+V/t6RdCSoJ6EzNmV/Eixm9xL1aK6ZbHywr5iwgM2VJz/H+fkT0f8nMgCCvDLQTaixjSqd0kNETWOyI1/SuD5F3rVQSWEIhOoEJs2sMoRihhLKUfpE5ih67OUZF01gFZFh4RVp2KiDuuHvdNfegJy3VfzReu0InG5m0wf7QzU1p+KJEl6ihJYifPkAInUBryjeM1B7zm6BjA/FmTtLNp+hQMpuvP5oX0U2UMzfdpo7cilyj8r6s+2G9AGXRl8QP1wayoYyJ4shzf9JP+bvydEhNe5I49tBpFNZ7NK2O1RFJ9vM7thoBTviFlvRKrsb8gRhQIb1SF8+r+W+lL8uNWTNM1979cmVmzI0wlpiuk92JrKqZt9OP/21nE+sf80bSa1AEWTXpD5aRoMZWk4mTBI5XtEhN3RsUkoHfHkepKQX3guLt70Y/pmtNfq+q1Ltzs80hL2hNn33HClgB81hiCo0nL5qwW/NyK6SLG5WyHD9sNuaqXfEPPjuN8lTLjQoblUqwFwUU7TcfZz6IGQ3d8r4JLwCueR2EV7B5Wqw0CSbNSqiTyP+eSY1Dfi1WgKHqCQMcgMQrk5NpV6KiTP7QnoaNc41xsuyMK7TyRNHuQzxV8+dV+Zm9dWFllGJNSpf5H02fxKSyPzKBUvaH7QrtJYA4OvSHGLakK3t7f7O/6WQKXnu18xve9rmpWZkh7f4uluDrLagksoVZLsWIqxLt9R5Yl7WcGClsq+eHbwJoNtd4p9Jw8CiEE9kL+bPnNTDA=
2017-03-23 15:48:52,238  INFO http-nio-8080-exec-1 (com.reapal.repalPay.ReapalPay:206) - 用户：13220482188;订单号：R0000020001170323970;融宝首次支付的返回结果是：{"bank_code":"CCB","bank_name":"建设银行","bind_id":"343546","merchant_id":"100000000000147","order_no":"R0000020001170323970","result_code":"0000","result_msg":"签约成功"}
2017-03-23 15:48:52,362 ERROR http-nio-8080-exec-1 (com.stone.common.utils.TokenUtil:72) - base64 gen token src :TK_20170322133507_2_320540
2017-03-23 15:48:52,364 ERROR http-nio-8080-exec-1 (com.stone.common.utils.TokenUtil:75) - base64 gen token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:52,370 ERROR http-nio-8080-exec-1 (com.stone.interceptor.MD5OutMessageInterceptor:48) - out : {"code":"0","result":{"rechargeNo":"R0000020001170323970","id":2,"addTime":1490160907000,"payWay":"4","token":"VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=","errorCode":"0000","errorMsg":"签约成功","requestid":null,"showBox":1,"paySucByH5":0,"reapalCertify":null,"bankType":"CCB"},"errorType":null,"errorMsg":"正常","success":true}
2017-03-23 15:48:52,371  INFO http-nio-8080-exec-1 (com.stone.interceptor.AuthFilter:136) - execute[/stone-rest/rest/project/investV2.json]spend time : 1848
2017-03-23 15:48:56,658  INFO http-nio-8080-exec-5 (com.stone.interceptor.AuthFilter:86) - request url : /stone-rest/rest/project/investV2.json, params : {deviceType=2, amount=100, interestCoupon=, phoneSystemVersion=5.1.1, bankCardNo=6214242710498301, rechargeNo=R0000020001170323970, bankType=CCB, supperUserId=, mobile=13220482188, channel=default_channel, payWay=4, versionName=1.2.0, cardNo=210302196001012114, versionCode=75, token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=, realName=韩梅梅, requestid=, registrationId=, userIp=192.168.202.244, deviceSerialId=eb68672e7cbb8f11, validatecode=123456, id=1}
2017-03-23 15:48:56,659 ERROR http-nio-8080-exec-5 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:56,661  INFO http-nio-8080-exec-5 (org.apache.cxf.interceptor.LoggingInInterceptor:250) - Inbound Message
----------------------------
ID: 18
Address: http://139.196.41.47:8080/stone-rest/rest/project/investV2.json
Encoding: UTF-8
Http-Method: POST
Content-Type: application/x-www-form-urlencoded
Headers: {connection=[Keep-Alive], Content-Length=[451], content-type=[application/x-www-form-urlencoded], expect=[100-continue], host=[139.196.41.47:8080]}
--------------------------------------
2017-03-23 15:48:56,662  INFO http-nio-8080-exec-5 (org.apache.cxf.jaxrs.utils.FormUtils:173) - bankCardNo=6214242710498301&versionCode=75&token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA%3D&mobile=13220482188&realName=%E9%9F%A9%E6%A2%85%E6%A2%85&deviceType=2&rechargeNo=R0000020001170323970&userIp=192.168.202.244&deviceSerialId=eb68672e7cbb8f11&supperUserId=&phoneSystemVersion=5.1.1&versionName=1.2.0&amount=100&bankType=CCB&id=1&requestid=&payWay=4&registrationId=&validatecode=123456&channel=default_channel&interestCoupon=&cardNo=210302196001012114
2017-03-23 15:48:56,664 ERROR http-nio-8080-exec-5 (com.stone.rest.service.project.impl.RestProjectServiceImpl:1625) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
********************代签名字符串为begin*******/ncheck_code=123456&merchant_id=100000000000147&order_no=R0000020001170323970&version=3.1.3g0be2385657fa355af68b74e9913a1320af82gb7ae5f580g79bffd04a402ba8f/n********************代签名字符串end*******
********************签名sign为：337befdc1d8abab75616b525780bece9
2017-03-23 15:48:56,686  INFO http-nio-8080-exec-5 (com.reapal.utils.Decipher:61) - 融宝API 准备去加密和融宝交互的请求参数是:{"order_no":"R0000020001170323970","check_code":"123456","sign":"337befdc1d8abab75616b525780bece9","merchant_id":"100000000000147","version":"3.1.3","sign_type":"MD5"}
2017-03-23 15:48:56,689  INFO http-nio-8080-exec-5 (com.reapal.utils.Decipher:73) - 融宝API 密文key============>XDnjWZRxXI2whx/qo9R1Z5xGiWlZTuVoe/Mls6VQUGfHe5xJRiCxUFPA6gT5tgTrtkY0v1IxMbdK70fTswiTEx8eB/4bxZpOWbqv6PuxpW4TCeJRQXdztmyJCwtmNbEnHNaKnLtH8mVRPvHAN8zaxlYwUsqmg8CkRYlWPrJl3UtElcSfTwJTE6c0NBtHMbn1J6Da3x/Afz06XxN0HIoKakf5kew3SliWVeLmZ6SCtnDfXwzAXZrcvmrPpPYFzag01AzeO/GbGqp5N37VF4BUsh3nFuDREXDNqEs0GF6zaA9bKXaRzojZTVJngJFSLc1D7iX+OsGwsC1IeifFn6xXyQ==
2017-03-23 15:48:56,689  INFO http-nio-8080-exec-5 (com.reapal.utils.Decipher:74) - 融宝API ===========>5+VDLLLBX/px0X5/4fYniOWAU26X70NK8GXYjyLrJW/ApHaE6F8liFrWkBbfYIPxvGzy4Wd5GV/dvRywXWOSD7zCsu8vfsry3uJszwuK7xgN04ZxIUF4RaKXb1VsZlJcquLK4g6J5YZcYQb3HayLtRuhDQbXNRaBz3hjzms0LMyuL884zoqkxko/3rcQdYLzMvD7EnxImACpe8ISSmjeBiGILyZufuN8xU2f37ZHjco=
2017-03-23 15:48:56,871  INFO http-nio-8080-exec-3 (com.stone.interceptor.AuthFilter:86) - request url : /stone-rest/rest/user/getCouponsForInvest.json, params : {deviceType=2, phoneSystemVersion=5.1.1, supperUserId=, registrationId=, channel=default_channel, deviceSerialId=eb68672e7cbb8f11, versionName=1.2.0, versionCode=75, token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=}
2017-03-23 15:48:56,872 ERROR http-nio-8080-exec-3 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:56,874  INFO http-nio-8080-exec-3 (org.apache.cxf.interceptor.LoggingInInterceptor:250) - Inbound Message
----------------------------
ID: 19
Address: http://139.196.41.47:8080/stone-rest/rest/user/getCouponsForInvest.json
Encoding: UTF-8
Http-Method: POST
Content-Type: application/x-www-form-urlencoded
Headers: {connection=[Keep-Alive], Content-Length=[201], content-type=[application/x-www-form-urlencoded], expect=[100-continue], host=[139.196.41.47:8080]}
--------------------------------------
2017-03-23 15:48:56,875  INFO http-nio-8080-exec-3 (org.apache.cxf.jaxrs.utils.FormUtils:173) - versionName=1.2.0&versionCode=75&token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA%3D&deviceType=2&deviceSerialId=eb68672e7cbb8f11&supperUserId=&registrationId=&channel=default_channel&phoneSystemVersion=5.1.1
2017-03-23 15:48:56,876 ERROR http-nio-8080-exec-3 (com.stone.rest.service.user.impl.RestUserServiceImpl:2056) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
返回确认支付结果post==========>{"data":"hzgC9wGAuEHXDJ16TDh7BqLYkIhVaUiz1tFSb9OJuGJ8aY4chvQ2IeklFki82ZsEtaHCWLmAwEDmqIDVFp65sBYmlCUd2ESZfTDGG+Ju8C/mAHdK3WuETU0H6kZLJK12eVTGJG4NCPKRFQfDffYPoMcNiSBaj9SuE6UMlZ9aLE9+7baILRX04CLchMa7PgMxK+4Bzb83u/kL1fZu/QtU9UYsBu/u4kB7CH4k4DS6wzrpVJ5flLuJU5UdhEbLyOiG5EocttM7/VmG2jQ4rvxLW9+B77to9BiCSs5amaTl0vj/Blm+wXvohdPoEnz/Fy5Sf3h6kjdwbxndCd5/R2/8hGrby/DY7n1+r2I2cB2p6HpJeIDKo3621yw3rOH99T8T","encryptkey":"iaGunVVx28mgmfyv+o53roB2SXsvk2q/ts33bHzt8CfjkvxLTHCNT2fKm9Hx7FrIzCyF5QKv1CS+AHaBq5JQz5WZ26KmAHsykjcuewsqsZ28b6YG5Gl2nwDFcDd0Dg42t+qlHqj1YAeBmdXdTWudJcY0LXsaJuEzXuIGlNHsubckbozH/5aSN6+TIIoB/ZT/rybpT0aYSQGFU1RQL+/Ty6UES4t2mifsiEIF2TKrrnkTKyxFv5txLb2+p7rUcNpRBjZ/r78rSk8OACNF7JWsxFqwfLyJgYkiYHz99W0UsMh1/rLos2aNF6SINNPXQY5edjLYKH//gfsml3ne7pPiIg==","merchant_id":"100000000000147"}

2017-03-23 15:48:56,929 ERROR http-nio-8080-exec-3 (com.stone.interceptor.MD5OutMessageInterceptor:48) - out : {"code":"0","result":[{"categoryId":2,"id":8,"userId":2,"title":"新人注册奖励红包","subtitle":null,"content":"新人注册奖励红包","amount":10.00,"interestRate":null,"minDue":0,"minInvest":1000.00,"status":0,"expireTime":1495344907000},{"categoryId":2,"id":9,"userId":2,"title":"新人购买奖励红包","subtitle":null,"content":"新人购买奖励红包","amount":40.00,"interestRate":null,"minDue":0,"minInvest":5000.00,"status":0,"expireTime":1495344907000},{"categoryId":2,"id":10,"userId":2,"title":"新人专享红包","subtitle":null,"content":"新人专享红包","amount":50.00,"interestRate":null,"minDue":60,"minInvest":5000.00,"status":0,"expireTime":1495344907000},{"categoryId":2,"id":11,"userId":2,"title":"新人专享红包","subtitle":null,"content":"新人专享红包","amount":100.00,"interestRate":null,"minDue":60,"minInvest":10000.00,"status":0,"expireTime":1495344907000},{"categoryId":2,"id":12,"userId":2,"title":"新人专享红包","subtitle":null,"content":"新人专享红包","amount":345.00,"interestRate":null,"minDue":150,"minInvest":50000.00,"status":0,"expireTime":1495344907000},{"categoryId":2,"id":13,"userId":2,"title":"新人专享红包","subtitle":null,"content":"新人专享红包","amount":218.00,"interestRate":null,"minDue":120,"minInvest":20000.00,"status":0,"expireTime":1495344907000},{"categoryId":2,"id":14,"userId":2,"title":"新人专享红包","subtitle":null,"content":"新人专享红包","amount":125.00,"interestRate":null,"minDue":90,"minInvest":10000.00,"status":0,"expireTime":1495344907000}],"errorType":null,"errorMsg":"正常","success":true}
2017-03-23 15:48:56,931  INFO http-nio-8080-exec-3 (com.stone.interceptor.AuthFilter:136) - execute[/stone-rest/rest/user/getCouponsForInvest.json]spend time : 58
解密确认支付返回的数据==========>{"bank_card_type":"0","bank_code":"CCB","bank_name":"建设银行","bind_id":"343546","card_last":"8301","merchant_id":"100000000000147","order_no":"R0000020001170323970","phone":"13220482188","result_code":"0000","result_msg":"扣款成功","trade_no":"10170323000073104"}
2017-03-23 15:48:56,988  INFO http-nio-8080-exec-5 (com.reapal.repalPay.ReapalPay:354) - 用户:2;订单号R0000020001170323970调用确认支付返回数据是{"bank_card_type":"0","bank_code":"CCB","bank_name":"建设银行","bind_id":"343546","card_last":"8301","merchant_id":"100000000000147","order_no":"R0000020001170323970","phone":"13220482188","result_code":"0000","result_msg":"扣款成功","trade_no":"10170323000073104"}
扣款成功
2017-03-23 15:48:56,989  INFO http-nio-8080-exec-5 (com.reapal.repalPay.ReapalPay:378) - 用户:2;订单号R0000020001170323970确认支付成功，准备返回数据
2017-03-23 15:48:56,990 ERROR http-nio-8080-exec-5 (com.stone.interceptor.MD5OutMessageInterceptor:48) - out : {"code":"0","result":{"rechargeNo":"R0000020001170323970","id":0,"addTime":null,"payWay":"4","token":"VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=","errorCode":"0000","errorMsg":"扣款成功","requestid":null,"showBox":0,"paySucByH5":null,"reapalCertify":null,"bankType":null},"errorType":null,"errorMsg":"正常","success":true}
2017-03-23 15:48:56,992  INFO http-nio-8080-exec-5 (com.stone.interceptor.AuthFilter:136) - execute[/stone-rest/rest/project/investV2.json]spend time : 332
2017-03-23 15:48:59,266  INFO http-nio-8080-exec-2 (com.stone.interceptor.AuthFilter:86) - request url : /stone-rest/rest/user/redPointV2.json, params : {deviceType=2, phoneSystemVersion=5.1.1, supperUserId=, registrationId=, channel=default_channel, deviceSerialId=eb68672e7cbb8f11, versionName=1.2.0, versionCode=75, token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=}
2017-03-23 15:48:59,267 ERROR http-nio-8080-exec-2 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,270  INFO http-nio-8080-exec-2 (org.apache.cxf.interceptor.LoggingInInterceptor:250) - Inbound Message
----------------------------
ID: 20
Address: http://139.196.41.47:8080/stone-rest/rest/user/redPointV2.json
Encoding: UTF-8
Http-Method: POST
Content-Type: application/x-www-form-urlencoded
Headers: {connection=[Keep-Alive], Content-Length=[201], content-type=[application/x-www-form-urlencoded], expect=[100-continue], host=[139.196.41.47:8080]}
--------------------------------------
2017-03-23 15:48:59,272  INFO http-nio-8080-exec-2 (org.apache.cxf.jaxrs.utils.FormUtils:173) - versionName=1.2.0&versionCode=75&token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA%3D&deviceType=2&deviceSerialId=eb68672e7cbb8f11&supperUserId=&registrationId=&channel=default_channel&phoneSystemVersion=5.1.1
2017-03-23 15:48:59,285 ERROR http-nio-8080-exec-2 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,287  INFO http-nio-8080-exec-6 (com.stone.interceptor.AuthFilter:86) - request url : /stone-rest/rest/user/userAccountAssets.json, params : {deviceType=2, phoneSystemVersion=5.1.1, supperUserId=, registrationId=, channel=default_channel, deviceSerialId=eb68672e7cbb8f11, versionName=1.2.0, versionCode=75, token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=}
2017-03-23 15:48:59,287 ERROR http-nio-8080-exec-6 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,288  INFO http-nio-8080-exec-6 (org.apache.cxf.interceptor.LoggingInInterceptor:250) - Inbound Message
----------------------------
ID: 21
Address: http://139.196.41.47:8080/stone-rest/rest/user/userAccountAssets.json
Encoding: UTF-8
Http-Method: POST
Content-Type: application/x-www-form-urlencoded
Headers: {connection=[Keep-Alive], Content-Length=[201], content-type=[application/x-www-form-urlencoded], expect=[100-continue], host=[139.196.41.47:8080]}
--------------------------------------
2017-03-23 15:48:59,289  INFO http-nio-8080-exec-6 (org.apache.cxf.jaxrs.utils.FormUtils:173) - versionName=1.2.0&versionCode=75&token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA%3D&deviceType=2&deviceSerialId=eb68672e7cbb8f11&supperUserId=&registrationId=&channel=default_channel&phoneSystemVersion=5.1.1
2017-03-23 15:48:59,290 ERROR http-nio-8080-exec-6 (com.stone.rest.service.user.impl.RestUserServiceImpl:2056) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,379  INFO http-nio-8080-exec-10 (com.stone.interceptor.AuthFilter:86) - request url : /stone-rest/rest/project/queryInProgressProjectV3.json, params : {deviceType=2, phoneSystemVersion=5.1.1, supperUserId=, registrationId=, channel=default_channel, deviceSerialId=eb68672e7cbb8f11, versionName=1.2.0, versionCode=75, token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=}
2017-03-23 15:48:59,381 ERROR http-nio-8080-exec-10 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,380  INFO http-nio-8080-exec-7 (com.stone.interceptor.AuthFilter:86) - request url : /stone-rest/rest/message/getPopupAndSuspend.json, params : {deviceType=2, phoneSystemVersion=5.1.1, pos=1, supperUserId=, registrationId=, channel=default_channel, deviceSerialId=eb68672e7cbb8f11, versionName=1.2.0, versionCode=75, token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=}
2017-03-23 15:48:59,382 ERROR http-nio-8080-exec-7 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,380  INFO http-nio-8080-exec-8 (com.stone.interceptor.AuthFilter:86) - request url : /stone-rest/rest/user/userAccountAssets.json, params : {deviceType=2, phoneSystemVersion=5.1.1, supperUserId=, registrationId=, channel=default_channel, deviceSerialId=eb68672e7cbb8f11, versionName=1.2.0, versionCode=75, token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=}
2017-03-23 15:48:59,382 ERROR http-nio-8080-exec-8 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,384  INFO http-nio-8080-exec-8 (org.apache.cxf.interceptor.LoggingInInterceptor:250) - Inbound Message
----------------------------
ID: 22
Address: http://139.196.41.47:8080/stone-rest/rest/user/userAccountAssets.json
Encoding: UTF-8
Http-Method: POST
Content-Type: application/x-www-form-urlencoded
Headers: {connection=[Keep-Alive], Content-Length=[201], content-type=[application/x-www-form-urlencoded], expect=[100-continue], host=[139.196.41.47:8080]}
--------------------------------------
2017-03-23 15:48:59,385  INFO http-nio-8080-exec-9 (com.stone.interceptor.AuthFilter:86) - request url : /stone-rest/rest/message/getPopupAndSuspend.json, params : {deviceType=2, phoneSystemVersion=5.1.1, pos=2, supperUserId=, registrationId=, channel=default_channel, deviceSerialId=eb68672e7cbb8f11, versionName=1.2.0, versionCode=75, token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=}
2017-03-23 15:48:59,386 ERROR http-nio-8080-exec-9 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,386  INFO http-nio-8080-exec-8 (org.apache.cxf.jaxrs.utils.FormUtils:173) - versionName=1.2.0&versionCode=75&token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA%3D&deviceType=2&deviceSerialId=eb68672e7cbb8f11&supperUserId=&registrationId=&channel=default_channel&phoneSystemVersion=5.1.1
2017-03-23 15:48:59,390 ERROR http-nio-8080-exec-8 (com.stone.rest.service.user.impl.RestUserServiceImpl:2056) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,392  INFO http-nio-8080-exec-7 (org.apache.cxf.interceptor.LoggingInInterceptor:250) - Inbound Message
----------------------------
ID: 23
Address: http://139.196.41.47:8080/stone-rest/rest/message/getPopupAndSuspend.json
Encoding: UTF-8
Http-Method: POST
Content-Type: application/x-www-form-urlencoded
Headers: {connection=[Keep-Alive], Content-Length=[207], content-type=[application/x-www-form-urlencoded], expect=[100-continue], host=[139.196.41.47:8080]}
--------------------------------------
2017-03-23 15:48:59,393  INFO http-nio-8080-exec-7 (org.apache.cxf.jaxrs.utils.FormUtils:173) - versionName=1.2.0&pos=1&versionCode=75&token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA%3D&deviceType=2&deviceSerialId=eb68672e7cbb8f11&supperUserId=&registrationId=&channel=default_channel&phoneSystemVersion=5.1.1
2017-03-23 15:48:59,393  INFO http-nio-8080-exec-10 (org.apache.cxf.interceptor.LoggingInInterceptor:250) - Inbound Message
----------------------------
ID: 24
Address: http://139.196.41.47:8080/stone-rest/rest/project/queryInProgressProjectV3.json
Encoding: UTF-8
Http-Method: POST
Content-Type: application/x-www-form-urlencoded
Headers: {connection=[Keep-Alive], Content-Length=[201], content-type=[application/x-www-form-urlencoded], expect=[100-continue], host=[139.196.41.47:8080]}
--------------------------------------
2017-03-23 15:48:59,414  INFO http-nio-8080-exec-10 (org.apache.cxf.jaxrs.utils.FormUtils:173) - versionName=1.2.0&versionCode=75&token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA%3D&deviceType=2&deviceSerialId=eb68672e7cbb8f11&supperUserId=&registrationId=&channel=default_channel&phoneSystemVersion=5.1.1
2017-03-23 15:48:59,414 ERROR http-nio-8080-exec-10 (com.stone.rest.service.project.impl.RestProjectServiceImpl:1625) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,405  INFO http-nio-8080-exec-2 (com.stone.rest.service.user.impl.RestUserServiceV2Impl:384) - 小红点新,uid=> 2; 红包，加息券，现金券=> 7,0,0,0
2017-03-23 15:48:59,429 ERROR http-nio-8080-exec-2 (com.stone.interceptor.MD5OutMessageInterceptor:48) - out : {"code":"0","result":"7,0,0,0","errorType":null,"errorMsg":"正常","success":false}
2017-03-23 15:48:59,405 ERROR http-nio-8080-exec-7 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,401  INFO http-nio-8080-exec-9 (org.apache.cxf.interceptor.LoggingInInterceptor:250) - Inbound Message
----------------------------
ID: 25
Address: http://139.196.41.47:8080/stone-rest/rest/message/getPopupAndSuspend.json
Encoding: UTF-8
Http-Method: POST
Content-Type: application/x-www-form-urlencoded
Headers: {connection=[Keep-Alive], Content-Length=[207], content-type=[application/x-www-form-urlencoded], expect=[100-continue], host=[139.196.41.47:8080]}
--------------------------------------
2017-03-23 15:48:59,431  INFO http-nio-8080-exec-9 (org.apache.cxf.jaxrs.utils.FormUtils:173) - versionName=1.2.0&pos=2&versionCode=75&token=VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA%3D&deviceType=2&deviceSerialId=eb68672e7cbb8f11&supperUserId=&registrationId=&channel=default_channel&phoneSystemVersion=5.1.1
2017-03-23 15:48:59,431 ERROR http-nio-8080-exec-9 (com.stone.common.utils.TokenUtil:90) - get userId : 2 from token :VEtfMjAxNzAzMjIxMzM1MDdfMl8zMjA1NDA=
2017-03-23 15:48:59,433  INFO http-nio-8080-exec-2 (com.stone.interceptor.AuthFilter:136) - execute[/stone-rest/rest/user/redPointV2.json]spend time : 164
2017-03-23 15:48:59,464 ERROR http-nio-8080-exec-10 (com.stone.rest.service.project.impl.RestProjectServiceImpl:264) - invCount : 0
2017-03-23 15:48:59,517 ERROR http-nio-8080-exec-9 (com.stone.interceptor.MD5OutMessageInterceptor:48) - out : {"code":"0","result":{"sAdvPopup":null,"sAdvSuspend":{"id":1,"name":"一鼎活动","url":"{\"url\":\"https://www.baidu.com/\",\"islogin\":\"0\"}","img":"20170321/58d13f0d47c47.jpg","userType":0,"startTime":1490108164000,"endTime":1501430400000,"userId":1,"createTime":1490108173,"updateTime":0,"isDelete":0,"status":0}},"errorType":null,"errorMsg":"正常","success":false}
2017-03-23 15:48:59,518  INFO http-nio-8080-exec-9 (com.stone.interceptor.AuthFilter:136) - execute[/stone-rest/rest/message/getPopupAndSuspend.json]spend time : 118
2017-03-23 15:48:59,529 ERROR http-nio-8080-exec-10 (com.stone.service.redis.core.basic.util.serialize.ListTranscoder:34) - Caught IOException decoding %d bytes of data
2017-03-23 15:48:59,529  INFO http-nio-8080-exec-10 (com.stone.rest.service.project.impl.RestProjectServiceImpl:302) - 从redis获得已售罄产品
2017-03-23 15:48:59,530 ERROR http-nio-8080-exec-10 (com.stone.service.redis.core.basic.util.serialize.ListTranscoder:34) - Caught IOException decoding %d bytes of data
2017-03-23 15:48:59,530  INFO http-nio-8080-exec-10 (com.stone.rest.service.project.impl.RestProjectServiceImpl:331) - 从redis查询已还款
2017-03-23 15:48:59,536 ERROR http-nio-8080-exec-6 (com.stone.interceptor.MD5OutMessageInterceptor:48) - out : {"code":"0","result":{"id":2,"accountTotal":0.0000,"waitAmount":0.0000,"waitCapital":0.00,"waitInterest":0.00,"totalInvestCapital":0,"totalInvestInterest":0,"handOutCapitail":0,"handOutWallet":0,"totalUnreadCoupons":7},"errorType":null,"errorMsg":"正常","success":true}
2017-03-23 15:48:59,537  INFO http-nio-8080-exec-6 (com.stone.interceptor.AuthFilter:136) - execute[/stone-rest/rest/user/userAccountAssets.json]spend time : 249
2017-03-23 15:48:59,539 ERROR http-nio-8080-exec-7 (com.stone.interceptor.MD5OutMessageInterceptor:48) - out : {"code":"0","result":{"sAdvPopup":null,"sAdvSuspend":{"id":1,"name":"一鼎活动","url":"{\"url\":\"https://www.baidu.com/\",\"islogin\":\"0\"}","img":"20170321/58d13f0d47c47.jpg","userType":0,"startTime":1490108164000,"endTime":1501430400000,"userId":1,"createTime":1490108173,"updateTime":0,"isDelete":0,"status":0}},"errorType":null,"errorMsg":"正常","success":false}
2017-03-23 15:48:59,540  INFO http-nio-8080-exec-7 (com.stone.interceptor.AuthFilter:136) - execute[/stone-rest/rest/message/getPopupAndSuspend.json]spend time : 149
2017-03-23 15:48:59,544 ERROR http-nio-8080-exec-8 (com.stone.interceptor.MD5OutMessageInterceptor:48) - out : {"code":"0","result":{"id":2,"accountTotal":0.0000,"waitAmount":0.0000,"waitCapital":0.00,"waitInterest":0.00,"totalInvestCapital":0,"totalInvestInterest":0,"handOutCapitail":0,"handOutWallet":0,"totalUnreadCoupons":7},"errorType":null,"errorMsg":"正常","success":true}
2017-03-23 15:48:59,545  INFO http-nio-8080-exec-8 (com.stone.interceptor.AuthFilter:136) - execute[/stone-rest/rest/user/userAccountAssets.json]spend time : 162
2017-03-23 15:48:59,569 ERROR http-nio-8080-exec-10 (com.stone.interceptor.MD5OutMessageInterceptor:48) - out : {"code":"0","result":[{"categoryId":0,"categoryName":"零钱喵-活期理财","categoryDesc":"","spList":[{"firstInvest":null,"isCountdown":null,"broadcastSummary":null,"broadcastUrl":null,"userPlatformSubsidy":null,"action":null,"moneyType":null,"id":-1,"able":null,"amount":null,"title":"零钱喵钱包","duration":1,"userInterest":6.35,"repaymentType":null,"countInterestType":null,"moneyMin":null,"moneyMax":null,"status":2,"termType":1,"percent":null,"investDirectionTitle":null,"repaymentSourceTitle":null,"endTime":null,"startTime":null,"currentSystemTime":null,"buyTimes":null,"newPreferential":3,"type":0,"collectamount":null,"isH5":0,"tagName":null,"pmList":null,"sProjectModelFund":null,"sFund":null,"fdList":null,"changePercent":null,"investDirectionDescr":null,"interestDesc":"年化利率","clickAction":1,"toUrl":"","lastmonths":null,"paySucByH5":null,"investmentDetailBeanList":null,"countInterestTypeText":null,"repaymentTypeText":null,"walletInDesc":null,"walletOutDesc":null,"walletName":null,"walletDesc":null,"InvestTimes":null,"sProjectModelEquityWithBLOBs":null,"type2":null,"acceptingBank":null,"sProjectModelEquityDynamicList":null,"sProjectModelSection":null,"sProjectModelIncrement":null,"investTimes":null}]},{"categoryId":1,"categoryName":"票票喵-定期理财","categoryDesc":"","spList":[{"firstInvest":null,"isCountdown":0,"broadcastSummary":null,"broadcastUrl":null,"userPlatformSubsidy":0.00,"action":null,"moneyType":null,"id":1,"able":200000.00,"amount":200000.00,"title":"一鼎网1号第1期","duration":69,"userInterest":15.00,"repaymentType":1,"countInterestType":2,"moneyMin":1,"moneyMax":100000,"status":2,"termType":1,"percent":0.00,"investDirectionTitle":"","repaymentSourceTitle":"","endTime":1496160000000,"startTime":1490103000000,"currentSystemTime":1490255339529,"buyTimes":null,"newPreferential":0,"type":2,"collectamount":null,"isH5":0,"tagName":"普通","pmList":null,"sProjectModelFund":null,"sFund":null,"fdList":null,"changePercent":null,"investDirectionDescr":null,"interestDesc":"年化利率","clickAction":null,"toUrl":null,"lastmonths":0,"paySucByH5":0,"investmentDetailBeanList":null,"countInterestTypeText":null,"repaymentTypeText":null,"walletInDesc":null,"walletOutDesc":null,"walletName":null,"walletDesc":null,"InvestTimes":null,"sProjectModelEquityWithBLOBs":null,"type2":null,"acceptingBank":"北京银行","sProjectModelEquityDynamicList":null,"sProjectModelSection":null,"sProjectModelIncrement":null,"investTimes":null}]}],"errorType":null,"errorMsg":"正常","success":true}
2017-03-23 15:48:59,571  INFO http-nio-8080-exec-10 (com.stone.interceptor.AuthFilter:136) - execute[/stone-rest/rest/project/queryInProgressProjectV3.json]spend time : 179



奎哥, 我想等这个时间你整理下后台的一些东西, 然后约个时间过去给那边