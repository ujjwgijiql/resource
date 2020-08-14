# 安装docker

## 1、 删除旧版docker组件
卸载旧版docker
```shell
$ rpm -qa | grep docker
docker-ce-19.03.12-3.el7.x86_64
docker-ce-cli-19.03.12-3.el7.x86_64
```

分别删除
```shell
$ yum -y remove docker-ce-19.03.12-3.el7.x86_64
$ yum -y remove docker-ce-cli-19.03.12-3.el7.x86_64
```

删除旧版docker组件
```shell
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
&nbsp;&nbsp;  

## 2、 安装docker-ce预备环境
```shell
$ sudo yum install -y yum-utils

$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
&nbsp;&nbsp;  

## 3、 安装docker-ce
```shell
sudo yum install docker-ce docker-ce-cli containerd.io
```
```shell
$ yum list docker-ce --showduplicates | sort -r
```
&nbsp;&nbsp;  

## 4、启动docker
```shell
$ sudo systemctl enable docker.service
$ sudo systemctl start docker
```
&nbsp;&nbsp;  

# 配置docker环境
```shell
$ echo "172.16.20.83 registry.storm.io" >> \/etc\/hosts && echo '{"registry-mirrors": ["https://kfwkfulq.mirror.aliyuncs.com","https://2lqq34jg.mirror.aliyuncs.com","https://pee6w651.mirror.aliyuncs.com","https://registry.docker-cn.com","http://hub-mirror.c.163.com"], "storage-driver":"devicemapper","bip": "192.168.10.1/24","insecure-registries":["registry.storm.io"],"dns": ["8.8.8.8","8.8.4.4"]}' > \/etc\/docker\/daemon.json && systemctl restart docker
```   

```shell
$ echo '{"registry-mirrors": ["https://kfwkfulq.mirror.aliyuncs.com","https://2lqq34jg.mirror.aliyuncs.com","https://pee6w651.mirror.aliyuncs.com","https://registry.docker-cn.com","http://hub-mirror.c.163.com"],"storage-driver":"overlay","log-driver":"json-file","log-opts": {"max-size":"1024m", "max-file":"4"},"insecure-registries":["registry.storm.io"]}' > \/etc\/docker\/daemon.json
$ systemctl restart docker
```
