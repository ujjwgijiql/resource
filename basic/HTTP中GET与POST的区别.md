# 99%的人都理解错了HTTP中GET与POST的区别
 原作者：[ WebTechGarden](https://mp.weixin.qq.com/s?__biz=MzI3NzIzMzg3Mw==&mid=100000054&idx=1&sn=71f6c214f3833d9ca20b9f7dcd9d33e4#rd)    
 &nbsp;    
GET和POST是HTTP请求的两种基本方法，要说它们的区别，接触过WEB开发的人都能说出一二。    
 &nbsp;    
最直观的区别就是GET把参数包含在URL中，POST通过request body传递参数。    
 &nbsp;    
你可能自己写过无数个GET和POST请求，或者已经看过很多权威网站总结出的他们的区别，你非常清楚知道什么时候该用什么。    
 &nbsp;    
当你在面试中被问到这个问题，你的内心充满了自信和喜悦。    
&nbsp;   
 ![soeasy](https://github.com/zhang-jh/resource/blob/master/basic/images/soeasy.jpg) 

    你轻轻松松的给出了一个“标准答案”：
* GET在浏览器回退时是无害的，而POST会再次提交请求。
* GET产生的URL地址可以被Bookmark，而POST不可以。
* GET请求会被浏览器主动cache，而POST不会，除非手动设置。
* GET请求只能进行url编码，而POST支持多种编码方式。
* GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
* GET请求在URL中传送的参数是有长度限制的，而POST么有。
* 对参数的数据类型，GET只接受ASCII字符，而POST没有限制。
* GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。
* GET参数通过URL传递，POST放在Request body中。    
（本标准答案参考自w3schools）     
&nbsp;   
 “很遗憾，这不是我们要的回答!”    
&nbsp;    

请告诉我真相。。。    
&nbsp;   
如果我告诉你GET和POST本质上没有区别你信吗？     
&nbsp;   
让我们扒下GET和POST的外衣，坦诚相见吧！    
&nbsp;   

