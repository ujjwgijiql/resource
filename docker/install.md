# 安装docker

## 1、 删除旧版docker组件
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
