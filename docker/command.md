## 删除docker容器
* 如何强制删除docker容器？有时我们可能会遇到docker中无法停止或删除容器服务的问题，执行删除命令无法删除docker的目录  
这里把 caf8ef20f3c1 换成你想要stop的容器ID  
```shell
$ ll /var/lib/docker/containers | grep caf8ef20f3c1
$ cd /var/lib/docker/containers
$ rm -rf caf8ef20f3c1c78f03a5844ee23abc1d7e44246f242292040f1ef288899d0cb8
```
这个时候我们会收到这样的报错
```shell
$ rm: 无法删除"/var/lib/docker/containers/caf8ef20f3c1c78f03a5844ee23abc1d7e44246f242292040f1ef288899d0cb8/secrets": 设备或资源忙
无法删除"/var/lib/docker/containers/caf8ef20f3c1c78f03a5844ee23abc1d7e44246f242292040f1ef288899d0cb8/shm": 设备
```
从报错我们可以看到“secrets”和“shm”共享挂载导致无法删除，首先找到挂载的位置，然后取消挂载后，再删除：
```shell
$ cat /proc/mounts | grep "docker" | grep "caf8ef20f3c1"
```
取消挂载，这是上面的挂载目录
```shell
$ umount /data/sys/var/docker/containers/caf8ef20f3c1c78f03a5844ee23abc1d7e44246f242292040f1ef288899d0cb8/secrets
$ umount /data/sys/var/docker/containers/caf8ef20f3c1c78f03a5844ee23abc1d7e44246f242292040f1ef288899d0cb8/sh
```
再次查看已经没有显示了
```shell
$ cat /proc/mounts |grep "docker" |grep "caf8ef20f3c1"
```
现在执行删除docker的目录：
```shell
cd /var/lib/docker/containers
rm -rf caf8ef20f3c1c78f03a5844ee23abc1d7e44246f242292040f1ef288899d0cb8
```
现在我们使用 rm 或 kill 来删除容器服务：
```shell
$ docker rm -f caf8ef20f3c1c
```
或
```shell
$ docker kill --signal=SIGINT caf8ef20f3c1
```
如果上面命令运行之后，出现hang住（卡住）现象，重启docker服务：
```shell
sudo service docker restart
```

## docker 停止删除镜像和容器
停止容器
```shell
$ docker stop $(docker ps -a | grep "Exited" | awk '{print $1 }')
```
删除退出的容器
```shell
$ docker rm $(docker ps -a | grep "Exited" | awk '{print $1 }')
```
删除空的镜像
```shell
$ docker rmi $(docker images | grep "none" | awk '{print $3}') 
```
删除所有容器
```shell
$ docker rm `docker ps -a -q`
```
删除所有镜像
```shell
docker rmi `docker images -q`
```
