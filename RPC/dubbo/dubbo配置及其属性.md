#dubbo配置及其属性参考文件，配置参考手册

* \<dubbo:service/\>
* \<dubbo:reference/\>
* \<dubbo:protocol/\>
* \<dubbo:registry/\>
* \<dubbo:monitor/\>
* \<dubbo:application/\>
* \<dubbo:module/\>
* \<dubbo:provider/\>
* \<dubbo:consumer/\>
* \<dubbo:method/\>
* \<dubbo:argument/\>
* \<dubbo:parameter/\>

这里以Xml配置为准，列举所有配置项，其它配置方式，请参见相应转换关系：属性配置，注解配置，API配置  
&nbsp;&nbsp;  

__注意：只有group，interface，version是服务的匹配条件，三者决定是不是同一个服务，其它配置项均为调优和治理参数。__  
&nbsp;&nbsp;  

所有配置项分为三大类，参见下表中的"作用"一列。  
* 服务发现：表示该配置项用于服务的注册与发现，目的是让消费方找到提供方。
* 服务治理：表示该配置项用于治理服务间的关系，或为开发测试提供便利条件。
* 性能调优：表示该配置项用于调优性能，不同的选项对性能会产生影响。  
&nbsp;&nbsp;

所有配置最终都将转换为URL表示，并由服务提供方生成，经注册中心传递给消费方，各属性对应URL的参数，参见配置项一览表中的"对应URL参数"列。  
&nbsp;&nbsp;

## \<dubbo:service/\>
服务提供者暴露服务配置：  
配置类：com.alibaba.dubbo.config.ServiceConfig  

|       标签       |   属性    | 对应URL<br>参数 |     类型    |是否<br>必填|   缺省值   |   作用  |      描述         |      兼容性    |
|:---------------:|:---------:|:--------------:|:-----------:|:---------:|----------- |:-------:|:-----------------|:--------------:|
| \<dubbo:servic> | interface |                | class       |    必填    |           | 服务发现 |服务接口名         | 1.0.0以上版本   |
| \<dubbo:servic> | ref       |                | object      |    必填    |           | 服务发现 |服务对象实现引用    | 1.0.0以上版本   |
| \<dubbo:servic> | version   | version        | string      |    可选<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;    | 0.0.0     | 服务发现<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |服务版本，建议使用两位数字版本，如：1.0，通常在接口不兼容时版本号才需要升级| 1.0.0以上版本  |
| \<dubbo:servic> | group     | group          | string      |    可选    |           | 服务发现 |服务分组，当一个接口有多个实现，可以用分组区分| 1.0.7以上版本  |
| \<dubbo:servic> | path      | <path>         | string      |    可选    |缺省为接口名| 服务发现 |服务路径 (注意：1.0不支持自定义路径，总是使用接口名，如果有1.0调2.0，配置服务路径可能不兼容)| 1.0.12以上版本  |
| \<dubbo:servic> | delay     | delay          | int         |    可选    |0          | 性能调优 |延迟注册服务时间(毫秒) ，设为-1时，表示延迟到Spring容器初始化完成时暴露服务| 1.0.14以上版本  |
| \<dubbo:servic> | timeout   | timeout        | int         |    可选    |1000       | 性能调优 |远程服务调用超时时间(毫秒)| 2.0.0以上版本  |
| \<dubbo:servic> | retries   | retries        | int         |    可选    |2          | 性能调优 |远程服务调用重试次数，不包括第一次调用，不需要重试请设为0| 2.0.0以上版本  |
| \<dubbo:servic> |connections| connections    | int         |    可选    |100        | 性能调优 |对每个提供者的最大连接数，rmi、http、hessian等短连接协议表示限制连接数，dubbo等长连接协表示建立的长连接个数| 2.0.0以上版本  |
| \<dubbo:servic> |loadbalance| loadbalance    | string      |    可选    |random     | 性能调优 |负载均衡策略，可选值：random,roundrobin,leastactive，分别表示：随机，轮循，最少活跃调用| 2.0.0以上版本  |
| \<dubbo:servic> |async      | async          | boolean     |    可选    |false      | 性能调优 |是否缺省异步执行，不可靠异步，只是忽略返回值，不阻塞执行线程| 2.0.0以上版本  |
| \<dubbo:servic> |stub       | stub           |class/boolean|    可选    |false      | 服务治理 |设为true，表示使用缺省代理类名，即：接口名 + Local后缀，服务接口客户端本地代理类名，用于在客户端执行本地逻辑，如本地缓存等，该本地代理类的构造函数必须允许传入远程代理对象，构造函数如：public XxxServiceLocal(XxxService xxxService)| 2.0.0以上版本  |
| \<dubbo:servic> |mock       | mock           |class/boolean|    可选    |false      | 服务治理 |设为true，表示使用缺省Mock类名，即：接口名 + Mock后缀，服务接口调用失败Mock实现类，该Mock类必须有一个无参构造函数，与Local的区别在于，Local总是被执行，而Mock只在出现非业务异常(比如超时，网络异常等)时执行，Local在远程调用之前执行，Mock在远程调用后执行。| 2.0.0以上版本  |
| \<dubbo:servic> |token      | token          |string/boolean|    可选    |false      | 服务治理 |令牌验证，为空表示不开启，如果为true，表示随机生成动态令牌，否则使用静态令牌，令牌的作用是防止消费者绕过注册中心直接访问，保证注册中心的授权功能有效，如果使用点对点调用，需关闭令牌功能| 2.0.0以上版本  |
