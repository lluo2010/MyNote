@SpringBootApplication
public class Application extends WebMvcConfigurerAdapter {
    private static final Logger logger = LoggerFactory.getLogger(Application.class);

    public static void main(String[] args) {
        SpringApplication springApplication = new SpringApplication(Application.class);
        //注册容器生命周期打印日志，便于脚本部署容器
        springApplication.addListeners((ApplicationListener<ApplicationReadyEvent>) event -> logger.warn("yiding-manager Application Ready!"));
        springApplication.addListeners((ApplicationListener<ApplicationFailedEvent>) event -> logger.warn("yiding-manager Application Failed!"));

        ConfigurableApplicationContext appContext = springApplication.run(args);
        appContext.addApplicationListener(event -> logger.warn("yiding-manager Context Started!"));
        appContext.addApplicationListener(event -> logger.warn("yiding-manager Context Refreshed!"));
        appContext.addApplicationListener(event -> logger.warn("yiding-manager Context Closed!"));
    }



    public Map<String, Object> verifyRecharges(List<SRechargeLog> sRechargeLogs){
        Map<String, Object> result = new HashMap<>();
        sRechargeLogs.parallelStream().forEach(sRechargeLog -> {
            try {
                String reapalResult = ReapalSubmit.queryRecharge(sRechargeLog.getRechargeNo());
                JSONObject jsonObject = JSONObject.parseObject(reapalResult);
                String code = (String) jsonObject.get("result_code");
                if (!"0000".equals(code)){
                    result.put(sRechargeLog.getRechargeNo(), jsonObject.get("result_msg"));
                    return;
                }

                Object fee = jsonObject.get("total_fee");
                if (new BigDecimal((String) fee).longValue() !=
                        sRechargeLog.getAmount().multiply(BigDecimal.valueOf(100)).longValue()
                    ){
                    result.put(sRechargeLog.getRechargeNo(), "Real amount: " + fee + ", System amount: " + sRechargeLog.getAmount());
                    return;
                }
            }catch (Exception ex){
                logger.error("AccountService verifyRecharges exception", ex);
                result.put(sRechargeLog.getRechargeNo(), "Has been exception");
            }
        });
        return result;
    }

    public Map<String, Object> checkAccountBalance(){
        Map<String, Object> result = new HashMap<>();
        List<InvestBalanceResp> investBalanceResps = sUserAccountMapper.checkAccountInvestBalance();
        List<WalletBalanceResp> walletBalanceResps = sUserAccountMapper.checkAccountWalletBalance();
        result.put("isInvestBalance", investBalanceResps.isEmpty());
        result.put("InvestBalances", investBalanceResps);
        result.put("isWalletBalance", walletBalanceResps.isEmpty());
        result.put("walletBalances", walletBalanceResps);

        return result;
    }

}


....

    public static TongDunRiskResult checkLoginRisk(String strBlackBox, String clientType, String strMobile, String strIP) {
        CLIENT_TYPE cType = getClientType(clientType);
        Map<String, Object> params = getCommonMap(strBlackBox, cType, EVENT_TYPE.LOGIN);
        params.put(KEY_IP_ADDRESS, StringUtils.isEmpty(strIP) ? "" : strIP);
        params.put(KEY_ACCOUNT_MOBILE, strMobile);
        //params.put("account_password", ""); //目前平台也没有
        //params.put("account_login", ""); //业务平台分配给用户的唯一标识,目前注册前没有
        params.put("state", "0");//密码校验结果：0表示账户及密码一致性校验成功，1表示账户及密码一致性校验失败

        TongDunRiskResult riskResult = checkRisk(params, EVENT_TYPE.LOGIN);

        return riskResult;
    }

    private static TongDunRiskResult checkRisk(Map<String, Object> params, EVENT_TYPE eventType) {
        TongDunRiskResult riskResult = TongDunRiskResult.UNKNOWN;
        if (params != null) {
            try {
                FraudApiResponse apiResp =  new FraudApiInvoker().invoke(params);
                if (apiResp == null) {
                    logger.warn("Failed to invoke tongDun API, eventType:" + eventType);
                    return TongDunRiskResult.UNKNOWN;
                }
                if (apiResp.getSuccess()) {
                    logger.debug("Query risk from tonDun successfully");
                    riskResult = apiResp.getFinal_score() >= SCORE_ABOVE_ACCEPT ? TongDunRiskResult.REJECT : TongDunRiskResult.ACCEPT;
                    logger.debug("Risk detail info:" + apiResp.toString());
                } else {
                    logger.debug("Failed to query risk from tongDun, eventType:" + eventType + ",reason:" + apiResp.getReason_code());
                }
            } catch (IOException e) {
                logger.warn("Exception to query risk from tonDun, eventType:" + eventType + e.getMessage());
            }
        }

        return riskResult;
    }


    private static Map<String, Object> getCommonMap(String strBlackBox, CLIENT_TYPE clientType, EVENT_TYPE eventType) {
        Map<String, Object> params = new HashMap<String, Object>();
        params.put(KEY_PARTNER_CODE, FraudConfig.PARTNER_CODE);
        String strSecretKey = FraudConfig.ANDROID_SECRET_KEY; //default android
        if (clientType==CLIENT_TYPE.IOS){
            strSecretKey = FraudConfig.IOS_SECRET_KEY;
        }else if (clientType==CLIENT_TYPE.WEB){
            strSecretKey = FraudConfig.WEB_SECRET_KEY;
        }
        params.put(KEY_SECRET_KEY, strSecretKey);
        params.put(KEY_BLACK_BOX, strBlackBox);
        String strEvent = FraudEventStrDictionary.getEventID(clientType, eventType);
        params.put(KEY_EVENT_ID, strEvent);
        return params;
    }


    private static CLIENT_TYPE getClientType(String strAppType) {
        if (StringUtils.isEmpty(strAppType)){
            return CLIENT_TYPE.UNKNOWN;
        }

        CLIENT_TYPE clientType = CLIENT_TYPE.UNKNOWN;
        if (strAppType.equalsIgnoreCase(BusinessConstants.APP_TYPE_ANDROID)) {
            clientType = CLIENT_TYPE.ANDROID;
        } else if (strAppType.equalsIgnoreCase(BusinessConstants.APP_TYPE_IOS)) {
            clientType = CLIENT_TYPE.IOS;
        }
        return clientType;
    }

}