

```shell
$ docker pull registry.storm.io/xianzhi/cloud-waf:20200928.0
$ docker save -0 pingan_update_20200928.tar.gz registry.storm.io/xianzhi/cloud-waf:20200928.0
$ docker save -o  pingan_update_20200928.tar.gz registry.storm.io/xianzhi/cloud-waf:20200928.0
$ docker load -i pingan_update_20200928.tar.gz
$ sz pingan_update_20200928.tar.gz
```



# 查看运行容器的docker run启动参数

日常工作中可能会有一种情况，为了方便，直接使用docker run启动一个容器运行，没有使用docker-compose这样的编排工具，也没有保存docker run的命令，导致我们在迁移或者有修改的时候，有大概率会出现不一致的情况。

那么，能不能找回之前的run命令呢，答案是可以的。



```shell
$ docker inspect container
```



# 安装pip

```shell
$ yum -y install epel-release
$ yum install python-pip -y
```



# 安装runlike

```shell
$ pip install --upgrade pip   # 升级pip
$ pip install runlike
```





# 使用runlike查看启动参数

格式：runlike -p <容器名>|<容器ID>
