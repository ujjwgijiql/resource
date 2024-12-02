# Linux journalctl命令详解（journalctl指令、journal命令）（systemd服务默认日志管理工具）

## Linux [Journalctl](https://so.csdn.net/so/search?q=Journalctl&spm=1001.2101.3001.7020)命令详解

​    Linux提供了一个强大的日志系统，它可以跟踪和记录系统的各种活动。在这个系统中，`journalctl`是一个非常重要的工具，用于查询和操作由systemd进程管理的日志。

### 1. Journalctl简介

   Systemd是Linux发行版的初始化系统，负责启动系统后的所有服务，并监视它们在系统运行期间的状态。Journal是Systemd的一部分，主要负责收集和存储日志数据。

​    journalctl是Journal的主要接口，提供丰富的功能来检索和显示日志条目。它能从磁盘上的二进制文件或者其他传输目标（如syslog）获取日志。

### 2. Journalctl基础使用

- 查看所有日志：

  ```shell
  # （分页输出）
  # journalctl
  
  # （不分页输出）
  # journalctl --no-pager
  ```

- 按时间倒序查看所有日志：

  ```shell
  # journalctl -r
  ```

- 查看最新的10条日志：

  ```shell
  # journalctl -n 10
  ```

- 实时查看新添加的日志条目：

  ```shell
  # journalctl -f
  ```



### 3. 过滤日志条目

`journalctl`提供了多种方式来过滤和查找特定的日志条目。

- 根据服务名称过滤：

  ```shell
  # journalctl -u nginx
  ```

- 根据进程ID过滤：

  ```shell
  # journalctl _PID=2001
  ```

- 根据优先级过滤（0-7，0表示最重要）：

  ```shell
  # journalctl -p err -b
  ```



### 4. 时间戳和日志轮转

​    对于大型系统，日志文件可能会非常大。为了解决这个问题，`journalctl`提供了日志轮转和时间戳功能。

​    日志轮转是一个过程，其中旧的日志条目被删除以释放空间给新的日志条目。默认情况下，Journal将根据配置在/var/log/journal中保存日志文件，并在磁盘空间不足时执行日志轮转。

​    时间戳允许你查找特定时间范围内的日志条目。例如：

```shell
# journalctl --since="2021-07-01" --until="2021-07-31 03:00"
```



### 5. 高级应用

​    尽管journalctl提供了丰富的功能，但在某些情况下，你可能需要更高级的工具或技术。例如，你可能需要将日志发送到远程服务器，或者在多个系统上集中处理日志。

​    对于这些需求，你可以考虑使用像Logstash、Graylog或Fluentd这样的日志聚合工具，它们可以与journalctl结合使用，提供更强大的功能。



### 6. `journalctl --help`指令文档

```shell
journalctl [选项...] [匹配项...]

查询日志。

选项:
     --system                显示系统日志
     --user                  显示当前用户的用户日志
  -M --machine=CONTAINER     对本地容器进行操作
  -S --since=DATE            显示不早于指定日期的条目
  -U --until=DATE            显示不晚于指定日期的条目
  -c --cursor=CURSOR         从指定的游标开始显示条目
     --after-cursor=CURSOR   显示指定游标之后的条目
     --show-cursor           在所有条目后打印游标
     --cursor-file=FILE      显示文件中游标后的条目并更新文件
  -b --boot[=ID]             显示当前启动或指定的启动
     --list-boots            显示有关记录启动的简洁信息
  -k --dmesg                 显示当前启动的内核消息日志
  -u --unit=UNIT             显示指定单元的日志
     --user-unit=UNIT        显示指定用户单元的日志
  -t --identifier=STRING     显示具有指定syslog标识符的条目
  -p --priority=RANGE        显示具有指定优先级的条目
     --facility=FACILITY...  显示具有指定设施的条目
  -g --grep=PATTERN          显示与PATTERN匹配的MESSAGE的条目
     --case-sensitive[=BOOL] 强制进行大小写敏感或不敏感的匹配
  -e --pager-end             在分页器中立即跳到末尾
  -f --follow                跟踪日志
  -n --lines[=INTEGER]       要显示的日志条目数量
     --no-tail               即使在跟踪模式下也显示所有行
  -r --reverse               先显示最新的条目
  -o --output=STRING         改变日志输出模式 (short, short-precise,
                               short-iso, short-iso-precise, short-full,
                               short-monotonic, short-unix, verbose, export,
                               json, json-pretty, json-sse, json-seq, cat,
                               with-unit)
     --output-fields=LIST    在verbose/export/json模式下选择要打印的字段
     --utc                   以协调世界时(UTC)表示时间
  -x --catalog               在可用的地方添加消息解释
     --no-full               缩略字段
  -a --all                   显示所有字段，包括长和不可打印的
  -q --quiet                 不显示信息消息和权限警告
     --no-pager              不将输出管道输出到分页器
     --no-hostname           抑制主机名字段的输出
  -m --merge                 显示所有可用日志的条目
  -D --directory=PATH        显示来自目录的日志文件
     --file=PATH             显示日志文件
     --root=ROOT             在根目录下操作文件
     --namespace=NAMESPACE   显示指定命名空间的日志数据
     --interval=TIME         更改FSS密封键的时间间隔
     --verify-key=KEY        指定FSS验证键
     --force                 使用--setup-keys覆盖FSS密钥对
命令：
  -h --help                  显示此帮助文本
     --version               显示包版本
  -N --fields                列出当前使用的所有字段名称
  -F --field=FIELD           列出指定字段采取的所有值
     --disk-usage            显示所有日志文件的总磁盘使用量
     --vacuum-size=BYTES     将磁盘使用量减少到指定大小以下
     --vacuum-files=INT      只保留指定数量的日志文件
     --vacuum-time=TIME      删除早于指定时间的日志文件
     --verify                验证日志文件的一致性
     --sync                  将未写入的日志消息同步到磁盘
     --relinquish-var        停止记录到磁盘，记录到临时文件系统
     --smart-relinquish-var  类似，但如果日志目录在根挂载上，则无操作
     --flush                 将所有日志数据从 /run 刷新到 /var
     --rotate                请求立即旋转日志文件
     --header                显示日志头信息
     --list-catalog          在目录中显示所有消息ID
     --dump-catalog          显示消息目录中的条目
     --update-catalog        更新消息目录数据库
     --setup-keys            生成新的FSS密钥对

有关详细信息，请参阅 journalctl(1) 手册页。
```

### 7. 补充

​    清空所有日志

​    清除所有的系统日志，可使用 `journalctl` 的 `--vacuum-time` 或 `--vacuum-size` 选项。

   1. 清除所有日志：

      ```shell
      # journalctl --vacuum-time=1s
      ```

      这将会删除所有时间戳早于现在的日志条目，基本上等同于清空所有日志。

      journalctl --vacuum-time 命令通常接受一个相对时间值，如 “1year”, “2months”, “3weeks”, “4days”, “1s” 等。这个命令会删除所有在指定时间长度之前的日志。

2. 清除超过特定大小的日志：

   ```shell
   # journalctl --vacuum-size=1
   ```

   这将删除所有日志，直到系统日志的总大小降到1（单位为BYTE）。

   可以使用`journalctl --disk-usage`查看日志占用空间大小。

   ```shell
   # journalctl --disk-usage
   ```

   

3. 如果以上两个都没有效果，可以采用暴力方式直接删除日志文件。

```shell
# systemctl stop systemd-journald
# rm -rf /var/log/journal/*
# systemctl start systemd-journald
```

​    暴力删除有风险，使用需谨慎。



#### 过滤掉某个服务日志

比如systemd服务日志太多太烦杂，可以过滤掉它：

```shell
# journalctl | grep -v systemd
```



## 注意事项

​    journal日志不会将程序输出的空行显示，日志会被压缩得满满当当。

​    journal日志不会自动持久化，重启系统后，历史日志将被清除。可参考journal日志持久化配置。



## 快速参考

### 查看最后一页日志：`journalctl -e`

### 查看日志占用存储空间大小：`journalctl --disk-usage`

### 过滤掉某个服务日志：journalctl | grep -v systemd