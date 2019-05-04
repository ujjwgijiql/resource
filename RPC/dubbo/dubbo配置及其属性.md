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

<style>
table th:first-of-type {
	width: 100px;
}
</style>
|       标签       |   属性    | 对应URL<br>参数 |     类型    |是否<br>必填|   缺省值   |   作用  |      描述         |      兼容性    |
|:---------------:|:---------:|:--------------:|:-----------:|:---------:|----------- |:-------:|:-----------------|:--------------:|
| \<dubbo:servic> | interface |                | class       |    必填    |           | 服务发现 |服务接口名         |  1.0.0以上版本  |
| \<dubbo:servic> | ref       |                | object      |    必填    |           | 服务发现 |服务对象实现引用    |  1.0.0以上版本  |
| \<dubbo:servic> | version   | version        | string      |    可选    | 0.0.0     | 服务发现 |服务版本，建议使用两位数字版本，如：1.0，通常在接口不兼容时版本号才需要升级|  1.0.0以上版本  |
