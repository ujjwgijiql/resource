# G1GCOption
&emsp;&emsp;G1 GC 给我们提供了很多的命令行选项，一类以布尔类型打头，“+”表示启用该选项，“-”表示关闭该选项。另一类采用数字赋值，不需要布尔类型打头。  
&emsp;&emsp;在cmd命令模式下输入java -X，输出如下内容，从输出可以看到GC日志可以通过-Xloggc:<file>方式输出。
```shell
# -X
    -Xmixed           混合模式执行 (默认)
    -Xint             仅解释模式执行
    -Xbootclasspath:<用 ; 分隔的目录和 zip/jar 文件>
                      设置搜索路径以引导类和资源
    -Xbootclasspath/a:<用 ; 分隔的目录和 zip/jar 文件>
                      附加在引导类路径末尾
    -Xbootclasspath/p:<用 ; 分隔的目录和 zip/jar 文件>
                      置于引导类路径之前
    -Xdiag            显示附加诊断消息
    -Xnoclassgc       禁用类垃圾收集
    -Xincgc           启用增量垃圾收集
    -Xloggc:<file>    将 GC 状态记录在文件中 (带时间戳)
    -Xbatch           禁用后台编译
    -Xms<size>        设置初始 Java 堆大小
    -Xmx<size>        设置最大 Java 堆大小
    -Xss<size>        设置 Java 线程堆栈大小
    -Xprof            输出 cpu 配置文件数据
    -Xfuture          启用最严格的检查, 预期将来的默认值
    -Xrs              减少 Java/VM 对操作系统信号的使用 (请参阅文档)
    -Xcheck:jni       对 JNI 函数执行其他检查
    -Xshare:off       不尝试使用共享类数据
    -Xshare:auto      在可能的情况下使用共享类数据 (默认)
    -Xshare:on        要求使用共享类数据, 否则将失败。
    -XshowSettings    显示所有设置并继续
    -XshowSettings:all
                      显示所有设置并继续
    -XshowSettings:vm 显示所有与 vm 相关的设置并继续
    -XshowSettings:properties
                      显示所有属性设置并继续
    -XshowSettings:locale
                      显示所有与区域设置相关的设置并继续

-X 选项是非标准选项, 如有更改, 恕不另行通知。
```
&nbsp;&nbsp;

## 1、-XX:+PrintGCDetails
&emsp;&emsp;该选项用于记录GC运行时的详细数据信息并输出，是最基本、使用最普遍的一个选项。  
这个选项适用于所有GC，输出内容主要包括新生成对象占用内存大小以及耗费时间、各年龄代的情况、每次回收的对应数据等。
&nbsp;&nbsp;
&nbsp;&nbsp;

## 2、-Xloggc
&emsp;&emsp;以文件的形式保存GC日志。  
可以在JVM的运行选项里添加-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(9071560k free), swap 19143876k(9693800k free)
CommandLine flags: -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC 
Heap
 PSYoungGen      total 76288K, used 27530K [0x000000076b500000, 0x0000000770a00000, 0x00000007c0000000)
  eden space 65536K, 42% used [0x000000076b500000,0x000000076cfe2850,0x000000076f500000)
  from space 10752K, 0% used [0x000000076ff80000,0x000000076ff80000,0x0000000770a00000)
  to   space 10752K, 0% used [0x000000076f500000,0x000000076f500000,0x000000076ff80000)
 ParOldGen       total 175104K, used 0K [0x00000006c1e00000, 0x00000006cc900000, 0x000000076b500000)
  object space 175104K, 0% used [0x00000006c1e00000,0x00000006c1e00000,0x00000006cc900000)
 Metaspace       used 8652K, capacity 8946K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K

```

