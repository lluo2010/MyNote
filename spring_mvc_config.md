# Spring MVC 配置











## Reference

---

1. [SpringMVC最基础配置](http://blog.csdn.net/clj198606061111/article/details/20492887)
1. [spring mvc 配置（xml配置详解）](http://www.newasp.net/tech/71609.html)
1. [SpringMVC基础配置(通过注解配置,非xml配置)](http://blog.csdn.net/u012702547/article/details/53674867)
1. [史上最全最强SpringMVC详细示例实战教程](http://www.admin10000.com/document/6436.html)
1. [手把手教你搭建SpringMVC——最小化配置](http://www.cnblogs.com/xing901022/p/5240044.html)
1. [spring MVC配置详解](http://www.cnblogs.com/superjt/p/3309255.html)
1. [Spring MVC配置文件的三个常用配置详解](http://www.cnblogs.com/fangqi/archive/2012/11/04/2748579.html)




处理器拦截器运行流程图

1. 正常流程:

2. 中断流程:



中断流程中，比如是HandlerInterceptor2中断的流程（preHandle返回false），此处仅调用它之前拦截器的preHandle返回true的afterCompletion方法。