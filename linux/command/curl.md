# curl不打印% Total信息
在shell中使用curl获取返回值的时候，会打印如下信息
```shell
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   219    0   219    0     0  43349      0 --:--:-- --:--:-- --:--:-- 73000
```
在curl的命令参数中，有个slient选项，在curl后面加上-s就可以了    
-s/--silent静音模式。不输出任何东西
