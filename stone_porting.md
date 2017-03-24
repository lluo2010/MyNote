


##  用户账户关联表s_user_account

账号编号和用户id关联. 包含用户账号相关的信息, 比如总的已还款本金,用户总金额,钱包总金额,待收总本金,待收总利息,总收入利息.

sUserAccountMapper

1. login时如果没有则插入

```
if(!versionName.isEmpty() && VerifyUtil.parseStrVersionCodeToDouble(versionName)==1){
                sUserAccount.setToWallet(0);
            }else{
                sUserAccount.setToWallet(1);
            }

			sUserAccountMapper.insertSelective(sUserAccount);
```

1. InvestV2的时候, 如果没有记录, 添加

```
SUserAccount sUserAccount = new SUserAccount();
sUserAccount.setUserId(sUser.getId());
sUserAccount.setAccountTotal(BigDecimal.ZERO);
sUserAccount.setAccountAble(BigDecimal.ZERO);
sUserAccount.setAccountFreeze(BigDecimal.ZERO);
sUserAccount.setWaitAmount(BigDecimal.ZERO);
sUserAccount.setWaitCapital(BigDecimal.ZERO);
sUserAccount.setWaitInterest(BigDecimal.ZERO);
sUserAccount.setIntegration(0);
sUserAccount.setWalletLastInterestTime(date);
sUserAccount.setAddTime(date);
sUserAccount.setAddUserId(sUser.getId());
sUserAccount.setModifyTime(date);
sUserAccount.setModifyUserId(sUser.getId());
sUserAccountMapper.insertSelective(sUserAccount);
```


1. 投资成功后(包括钱包充值)收到通知时(processPayNotify), 更新:

```
SUserAccount sUserAccount = sUserAccountMapper.selectByUserId(userId);
SUserAccount accountTemp1 = new SUserAccount();
accountTemp1.setId(sUserAccount.getId());
accountTemp1.setAccountTotal(sUserAccount.getAccountTotal().add(sRechargeLog.getAmount().add(redAmount)));
accountTemp1.setAccountAble(sUserAccount.getAccountAble().add(sRechargeLog.getAmount().add(redAmount)));
sUserAccountMapper.updateByPrimaryKeySelective(accountTemp1);
```

1. 钱包转出也会更新

1. 没看到回款时更新???????

sUserMapper

@Override
	public int updateByPrimaryKeySelective(SUser record) {
		return sUserMapper.updateByPrimaryKeySelective(record);
	}


updateByPrimaryKeySelective



sudo cp /usr/local/redis-3.2.8/src/mkreleasehdr.sh ./bin
sudo cp /usr/local/redis-3.2.8/src/redis-cli ./bin


redis-benchmark， redis-check-dump， redis-cli， redis-server拷贝到bin目录