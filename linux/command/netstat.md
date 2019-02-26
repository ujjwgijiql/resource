# netstat
在linux一般使用netstat 来查看系统端口使用情况步。    
netstat命令是一个监控TCP/IP网络的非常有用的工具，它可以显示路由表、实际的网络连接以及每一个网络接口设备。    
netstat命令的功能是显示网络连接、路由表和网络接口信息，可以让用户得知目前都有哪些网络连接正在运作。    
该命令的一般格式为：netstat [选项]    
```shell
netstat -nat |awk ‘{print $6}’|sort|uniq -c|sort -nr
```
## 1.netstat命令详解
常用的是 netstat -tnl | grep 443 （查看443端口是否被占用），如果有当前是root用户，用netstat -pnl | grep 443 （还可显示出占用本机443端口的进程PID）。    
netstat    
功能说明：显示网络状态。    
语　　法：netstat [-acCeFghilMnNoprstuvVwx] [-A<网络类型>][--ip]    
补充说明：利用netstat指令可让你得知整个Linux系统的网络情况。    
参　　数：    
* -a　或–all　　　　　　　　　　　 显示所有连线中的Socket。    
* -A　　　　　　　　　　　　　　　 <网络类型>或–<网络类型> 列出该网络类型连线中的相关地址。
* -c　或–continuous　　　　　　　 持续列出网络状态。
* -C　或–cache　　　　　　　　　　显示路由器配置的快取信息。
* -e　或–extend　　　　　　　　　 显示网络其他相关信息。
* -F　或 –fib　　　　　　　　　　 显示FIB。
* -g　或–groups　　　　　　　　　 显示多重广播功能群组组员名单。
* -h　或–help　　　　　　　　　　 在线帮助。
* -i　或–interfaces　　　　　　　显示网络界面信息表单。
* -l　或–listening　　　　　　　 显示监控中的服务器的Socket。
* -M　或–masquerade　　　　　　　显示伪装的网络连线。
* -n　或–numeric　　　　　　　　 直接使用IP地址，而不通过域名服务器。
* -N　或–netlink或–symbolic　　 显示网络硬件外围设备的符号连接名称。
* -o　或–timers　　　　　　　　　显示计时器。
* -p　或–programs　　　　　　　　显示正在使用Socket的程序识别码和程序名称。
* -r　或–route　　　　　　　　　 显示 Routing Table。
* -s　或–statistice　　　　　　 显示网络工作信息统计表。
* -t　或–tcp　　　　　　　　　　 显示TCP 传输协议的连线状况。
* -u　或–udp　　　　　　　　　　 显示UDP传输协议的连线状况。
* -v　或–verbose　　　　　　　　 显示指令执行过程。
* -V　或–version　　　　　　　　 显示版本信息。
* -w　或–raw　　　　　　　　　　 显示RAW传输协议的连线状况。
* -x　或–unix　　　　　　　　　　此参数的效果和指定”-A unix”参数相同。
* –ip　或–inet　　　　　　　　　 此参数的效果和指定”-A inet”参数相同。

