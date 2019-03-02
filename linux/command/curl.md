# curl不打印% Total信息
在shell中使用curl获取返回值的时候，会打印如下信息
```shell
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   219    0   219    0     0  43349      0 --:--:-- --:--:-- --:--:-- 73000
```
在curl的命令参数中，有个slient选项，在curl后面加上-s就可以了    
-s/--silent静音模式。不输出任何东西    
&nbsp;&nbsp;&nbsp;&nbsp;
#  shell curl 取得HTTP返回的状态码
```shell
curl -I -m 10 -o /dev/null -s -w %{http_code} www.baidu.com
```
* -I 仅测试HTTP头
* -m 10 最多查询10s
* -o /dev/null 屏蔽原有输出信息
* -s silent 模式，不输出任何东西
* -w %{http_code} 控制额外输出

如果此命令写入Shell 脚本的话，需注意
result_code=`curl -I -m 10 -o /dev/null -s -w %{http_code} www.baidu.com`
符号｀ 此符号不是单引号，是数字1旁边的符号，才能识别此命令。
