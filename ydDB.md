### 标产品表s_project
和标相关的信息，包括标的状态（在售，已售罄等）。



### 用户表s_user
包含用户的一些新(手机, 身份证,头像,生日,是否新手, 还款开关设置,支付密码, 极光推送id)等.


### 用户银行关联表s_user_bank
银行卡信息， 包含用户的id,和用户关联，也包含是否支付过（has_pay_success），支付渠道等信息， 还包括这张银行卡转入钱包的金额。


### 用户账户关联表s_user_account
用户账户关联的信息， 包含用户id,用户总金额，钱包总金额， 待收利息， 最后一次计息时间,是否开启到期转入钱包等等。



## 钱包相关

### 用户钱包年利率表s_user_wallet_annualized_rate.
钱包利率表， 可以标志某一天的利率。 如果表示某一天的利率，也可以是某一段时间的利率。

add_time为某一天，rate为年化利率

对应map为SUserWalletAnnualizedRateExtendMapper.


### 用户钱包利息表s_user_wallet_interest
包括用户id, 计息金额， 利率，利息，计息时间等。

钱包每日计息会插入一条, 如果不计息就不会有数据插入。

Java端不会对该表的利息，利率进行调整。

对应对象为SUserWalletInterest，对应map为SUserWalletInterestExtendMapper。 



### 用户钱包投资表s_user_wallet_investment


没看到Java有在用。

钱包转入操作插入的表是什么？



### 用户钱包记录表s_user_wallet_records

用户钱包记录表，包括转入和转出，用户id, recharge_no，trade_no，支付方式，金额，支付状态（未支付，已支付，支付失败）,支付失败码，转出状态（正常，交易中，等待处理）, 转入转出银行卡id,用户购买记录id,以及一些时间。
type为取出2又分两种， 1中是钱包提现，它的recharge_no为Tx开头，还有一种取出是用钱包购买标， 开头是以QB开头。

对应对象为SUserWalletRecords， 对应map为SUserWalletRecordsMapper，SUserWalletRecordsExtendMapper。

操作：

1. 钱包充值
1. 使用现金券转入钱包
1. 钱包提现申请。
1. 用钱包购买定期标时会插入。
1. 解绑银行卡， 这时候不需要真实交易.

*** 在SUserWalletRecordsExtendMapper里会做一些查询操作， 比如今天还有几次可提现？ 相关代码如下：

···
Map<String, Object> params = new HashMap<>();params.put("userId", userId);params.put("type", 2);params.put("status", 3);params.put("days", "days");List<SUserWalletRecords> uwrList = sUserWalletRecordsService.selectByMap(params);
···


### 用户钱包记录表s_user_money_records
表示某个用户往钱包里增加或者减少（其实就是提现或者充值）多少钱，包含用户id,提现或者充值的金额，状态（正常或者交易中）,add_time.

对应的map为SUserMoneyRecordsMapper。



### 钱包入款纪录表s_wallet_income_records



withdrawalCount
preWithdrawal.


## 定期产品

### 交易日志表s_recharge_log
包含用户id, 标id， 充值编码recharge_no, trade_no交易编码，支付方式（就是payway,融宝等），充值金额，状态（未支付，已支付），回调返回的系列参数，付款卡号，时间等等。

如果是通过钱包进行的购买， 则rechargeNo是以QB开头的。

对应的实体为SRechargeLog， map为SRechargeLogMapper。

操作：

1. investv2的时候会插入。
1. 支付渠道回调后会更新， 主要是更新状态（改为已支付）




## 交易相关

### 投资详情表s_investment_detail

包含购买的标的id, 用户id, 投资金额， 投标状态（未付款，投标成功，投标失败，已还款，还款中），付款卡号，标类型等。

对应对象SInvestmentDetail， 对应map为SInvestmentDetailExtendMapper,SInvestmentDetailMapper


操作：

1. 在第三方支付回调时（标，或者钱包购买）更改状态。



### 用户待收资金详情表s_user_due_detail

包含用户id,买的标，代收总金额，待收本金，待收利息,本息计数天数，本息计息开始时间，待收时间due_time， 付款卡号，待收状态（未还款，已还款，还款中），商户流水号，是否已经被转入钱包。

每购买一个标都有一条。


对应对象为SUserDueDetail， map为SUserDueDetailMapper.


动作：
第三方支付回调时调用sUserDueDetailMapper.insertSelective(sUserDueDetail)执行插入。 这时候会把待收利息的天数， 待收利息全部都算出来的e


start_time, due_time分别怎么计算。



### 还款详情表s_repayment_detail


标志每个标的还款信息， 包含本期总还款金额， 总的还款本金，总的还款利息， 待还时间repayment_time，还款状态（未还，已还，正在还，），是否已经发过短信，
repayment_time待还时间。

一个标对应一条记录， 其中status表示还款状态。

这个表是在PHP端.


### 账户详情表s_account_detail

购买标识会插入记录。包含账号id，以及本次的金额，以及时间等。



## 券包相关

### 用户红包表s_user_redenvelope
对应为SUserRedenvelope， 对应map为SUserRedenvelopeMapper.

对应表s_user_redenvelope

可以根据recharge_no是否为空看券是否被使用了？


###加息表s_user_interest_coupon


allocateNewUserRedEnvelope


sUserRedenvelopeMapper




## 消息

### 个人消息表s_message_personal
个人消息及对应的接收者， 有对应的消息内容id, 接收者id, 是否已读。


### 个人消息内容表s_message_personal_content

表示一条消息，包含id, url，ext(跳转动作)



## 钱包充值

### 充值请求提交
1. 更新s_User_bank
1. s_user_wallet_records插入记录， 转入，状态为未支付。

相关类:

1. UserServiceImpl


### 充值回调

更新下面表：

1. s_user:对是否通过身份，卡认证等进行更新。
1. s_user_wallet_records(用户钱包记录表):更新支付状态为已经支付,设置trade no.
1. s_user_bank(用户银行关联表):设置requestid, 更新该银行进来的钱包金额wallet_money,即在原来基础上加上这次充值的,以及支付时间，是否支付成功过等。
1. s_user_account(用户账户关联表):更新钱包总额，wallet_totle。
1. s_user_money_records:添加一条充值记录， 这个和s_user_wallet_records不同。

重点：

1. 更新s_user_wallet_records状态为已支付。
1. s_user_bank的转入金额wallet_money+新转入的金额。
1. s_user_account的钱包总额+新转进的金额。

相关类:

1. SUserWalletRecordsService
1. SUserWalletRecordsServiceImpl


### 钱包提现

某张卡能提现的金额等于这张卡存入的金额(肯定已经减去已经提现的)，以及已经收到的标的利息，以及钱包利息的总和。当然，如果这个值大于user_acount里的WalletTotle,只能取wallettolte,即该用户真实可用的。

更新下面表：

1. 因为是钱包提现，所以更新s_user_account中的钱包总额wallet_totle， 即钱包余额等于原来的余额-提现金额。
1. s_user_bank的wallet_money减去提现的金额，重新更新。
1. 如果减去该银行卡转入的金额还不够， 减去钱包利息，还不够减去产品收到的利息，都更新s_user_account, 字段wallet_interset,wallet_product_interest.

所以s_user_account中的钱包总额wallet_totle等于几张银行卡转入的钱的总和+标利息+钱包利息。




## 银行卡买标

### 购买

更新表如下：

1. 更新加息券/红包券中的字段,但此时红包状态还未设为已使用，要到支付成功后才设为已经使用。
1. 更新s_user中是否更新的字段以及其他字段。
1. 如果是新用户， 添加s_user_account.
1. 如果没有相关的bank记录， 记录s_user_bank.如果有卡， 更新卡信息。
1. 往s_recharge_log里添加交易记录， status为1未支付。其中包含投资的金额， 后面回调里用到金额，就是从recharege_log里获取的。

相关类：

ProjectService


### 支付回调

更新表如下：

1. 更新s_recharge_log里的status状态到2.
1. 将之前使用的券标记为已经使用。
1. 更新s_user_account, 修改待收本金，待收利息，待收总金额，都是加的操作。
1. 插入记录到s_account_detail,包含本次交易的资金, 这里插入两条记录， 一条表示充值，另一条表示投标，不知道为什么这样做。
1. 更新s_project的内容，主要是更新还剩多少可以卖， 是否售罄，如果是，更新状态。
1. 插入s_investment_detail一条记录。
1. 插入s_user_due_detail,包含待收本金，利息，待收总金额。
1. 更新s_user, 如果没有验证进行验证。
1. 更新s_user_bank, 更新total_money(总的投资金额),wait_money(全部金额，包括投资金额，包括红包，要收的利息等).

相关类：

1. ReapalPaymentNotifyAction
1. RechargeLogServiceImpl



## 钱包买标

### 购买

更新如下表：

1. 更新加息券/红包券中的字段,但此时红包状态还未设为已使用，要到支付成功后才设为已经使用。
1. 更新s_user中是否更新的字段以及其他字段。
1. 如果是新用户， 添加s_user_account.
1. 如果没有相关的bank记录， 记录s_user_bank.如果有卡， 更新卡信息。
1. 不需要更新s_recharge_log.


### 内部回调

通过钱包买标识不需要过融宝的， 所以所谓回调时程序内部自己调的。

更新如下表：

1. 将之前使用的券标记为已经使用。
1. 更新s_recharge_log里的status状态到2.
1. 将之前使用的券标记为已经使用。
1. 更新s_user_account, 修改待收本金，待收利息，待收总金额，都是加的操作。
1. 插入记录到s_account_detail,包含本次交易的资金, 这里插入两条记录， 一条表示充值，另一条表示投标，不知道为什么这样做。
1. 更新s_project的内容，主要是更新还剩多少可以卖， 是否售罄，如果是，更新状态。
1. 插入s_investment_detail一条记录。
1. 插入s_user_due_detail,包含待收本金，利息，待收总金额。
1. 更新s_user, 如果没有验证进行验证。
1. 更新s_user_wallet_records.
1. 更新用户钱包记录表s_user_money_records


相关类：

1. RechargeLogService



## 总资产相关


### 账户总资产

总资产=账户钱包余额+待收本金（标）+待收利息+正在转出的本金+正在转出的钱包钱。    即账号余额+待收标的金额+利息+正在转出的钱。其中：

1. 待收本金，待收利息在s_user_account中获取。
1. 正在转出的钱包钱在S_User_Wallet_Records获取。
1. 正在转出的本金（就是标到期直接回款到银行卡）,从s_user_due_detail中获取， 不过一鼎是转到钱包的， 所以这个始终会为0.

相关代码如下：

    userAccountAssetsBean.setAccountTotal(sUserAccount.getWalletTotle().add(sUserAccount.getWaitCapital()).add(sUserAccount.getWaitInterest()) .add(handOutCapitail).add(handOutWallet));



#### 累计收益

累计收益=待收资金表s_user_due_detail所有的利息（已经到期的和未到期的）


具体见函数JsonResult userAccountAssets





重要的表

1. s_user_wallet_records
1. s_user_bank
1. s_user_account
1. s_user_money_records
1. s_user_due_detail
1. s_investment_detail
1. s_account_detail
1. s_recharge_log
1. s_repayment_detail



理解Servlet过滤器(javax.servlet.Filter)

http://blog.csdn.net/microtong/article/details/5007170

Servlet中涉及到的Filter和Listener

http://www.jianshu.com/p/06a7b129ff31



人员|	开始时间
:-|:-
乔震|3/27号
孙郑|3/27号
查德坤|3/28号
沈万其|4/1号
赵玉佩|3/28号
朱程杰|3/27号
冯璇|4/5号
李震|4/12号
江一翰|4/14号
罗宏钏|3/27号




01030000



s_account_detail
s_user_account
s_user_wallet_records



江一翰账户信息
13516716278   user_id=16  投资 充值三次每次100元，交易三次每次100元，本金300，未使用劵。
查德坤账户信息
18758245161		user_id=5   未投资，未充值
本次事件相关数据表如下：
SELECT * FROM s_account_detail WHERE user_id in(16,5)        	// user_id,add_user_id,modify_user_id 用户账户详情

SELECT * from s_user_account WHERE user_id in(16,5) 		     	// user_id  用户账户（总量）

SELECT * FROM s_user_wallet_records  WHERE user_id  in(16,5) 	// user_id ,user_bank_id  钱包变化记录

SELECT * FROM s_user_bank WHERE user_id in(16,5) 						 	// user_id , mobile   //用户银行卡信息

SELECT * FROM s_user_due_detail WHERE user_id in(16,5) 		    // user_id , add_user_id ,modify_user_id  //代收资金

SELECT * FROM s_investment_detail WHERE user_id in(16,5);			//投资详情

SELECT * FROM s_recharge_log WHERE user_id in(16,5);					//交易日志

关联数据表（本次问题 ，未涉及到这些表，原因是未回款，未使用卷）
SELECT * FROM s_user_money_records WHERE user_id in(16,5) 		// no data  用户钱包记录，暂时未有数据，应该是回款后调用

SELECT * FROM s_user_interest_coupon WHERE user_id in(16,5) 	// user_id  ,modify_user_id,add_user_id=0 劵没用，无需处理。

SELECT * FROM s_user_redenvelope WHERE user_id in(16,5)       //user_id , add_user_id 抵扣卷没用，无需处理。

SELECT * from s_wallet_income_records



5Gbps以下的四层DDoS攻击防护.




1. 主备切换；
1. 可用区切换；
1. 高安全模式；
1. 日志管理；
1. 性能诊断；
1. 只读实例；
1. 灾备实例；
1. 恢复到时间点（开发中）


双机高可用版


不过10Gbps以上的攻击同样不少见


不过10Gbps以上的攻击同样h不少见