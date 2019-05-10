# linux安装redis 完整步骤

### 1、 获取redis资源
```shell
wget http://download.redis.io/releases/redis-5.0.4.tar.gz
```

### 2、解压
```shell
$ tar xzvf redis-5.0.4.tar.gz
```

### 3、安装
```shell
$ cd redis-5.0.4
$ make
$ cd src
$ make install PREFIX=/usr/share/redis-5.0.4
```

### 4、移动配置文件到安装目录下
