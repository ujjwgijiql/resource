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
