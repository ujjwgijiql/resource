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
* 日志输出时首先是操作系统、JRE相关信息，输出的是64位操作系统（windows-amd64），JRE (1.8.0_202-b08)版本。  
* 接下来是内存空间，物理内存16G，其中8.65G空闲，交换区18.25G,其中9.25G空闲。  
* 我们并没有指定过多的选项，JVM帮我们增加了一些默认选项，即：CommandLine flags: -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC  
* -XX:InitialHeapSize和-XX:MaxHeapSize就是我们比较熟悉的-Xms和-Xmx，它们允许我们指定JVM的初始和最大堆内存大小。  
* 自动添加的-XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation这三个选项和OOP有关。  OOP的全称是Ordinary Object Pointer，即普通对象指针。通常64位的JVM消耗的内存会比32位的大1.5倍，这是因为对象指针在64位架构下，长度会翻倍（更宽的寻址）。对于那些将要从32位平台移植到64位的应用来说，平白无故多了1/2的内存占用，作为一个开发者不愿意看到这种场景。所以从JDK1.6update14开始，64bitJVM正式支持了-XX:+UseCompressedOops这个可以压缩指针，直到节约内存占用的选项。CompressedOops的实现方式是在机器码中植入压缩与解压指令，可能会给JVM增加额外的开销。  
-XX:+UseCompressedClassPointers选项是在JDK8出现的，也是在永久区消失之后出现的新的选项，主要用于对类的原数据进行压缩。  
-XX:-UseLargePagesIndividualAllocation和Oops是一起使用的，在大页内存使用发生时这个选项也会自动启用。  
* -XX:+UseParallelGC 表示当我们没有指定GC时，由于JDK采用的是JDK8，所以默认采用的ParallelGC。  
&nbsp;&nbsp;
&nbsp;&nbsp;

## 3、-XX:+UseSerialGC
&emsp;&emsp;Serial GC是HotSpot客户端模式VM的默认GC，它是一种独占式的GC，即运行过程中会导致应用程序暂停（停顿），并且由于它是单线程执行的，所以停顿时间会比较长。Serial GC在老年代使用了一种叫做“Mark-Sweep-Compact”的算法，它适用于CPU核数较少且使用的内存空间较少的应用程序的场景。  
&emsp;&emsp;Serial GC和Parallel GC的差别是一个采用单线程，另一个采用多线程的处理方式。  
&emsp;&emsp;在JVM的运行选项里添加-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log -XX:+UseSerialGC
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(8948552k free), swap 19143876k(9518196k free)
CommandLine flags: -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseSerialGC 
Heap
 def new generation   total 78656K, used 30785K [0x00000006c1e00000, 0x00000006c7350000, 0x0000000716950000)
  eden space 69952K,  44% used [0x00000006c1e00000, 0x00000006c3c104e8, 0x00000006c6250000)
  from space 8704K,   0% used [0x00000006c6250000, 0x00000006c6250000, 0x00000006c6ad0000)
  to   space 8704K,   0% used [0x00000006c6ad0000, 0x00000006c6ad0000, 0x00000006c7350000)
 tenured generation   total 174784K, used 0K [0x0000000716950000, 0x0000000721400000, 0x00000007c0000000)
   the space 174784K,   0% used [0x0000000716950000, 0x0000000716950000, 0x0000000716950200, 0x0000000721400000)
 Metaspace       used 8652K, capacity 8950K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
```
输出日志声明了使用-XX:+UseSerialGC选项，其它选项没有变化，对应的输出由Parallel GC的“PSYoungGen”变化成了“def new generation”，这个主要是由于收集器的不同而造成的，来看一下规律。
* 串行收集器：即输出 DefNew，是使用-XX:+UseSerialGC（年轻代、老年代都用串行回收收集器）运行后输出的。
* 并行收集器：即输出 ParNew，是使用-XX:+UseParNewGC(年轻代使用并行收集器，老年代用串行回收收集器) 或者-XX:+UseConcMarkSweepGC(年轻代使用并行收集器，老年代使用CMS)运行后输出的。
* PSYoungGen：是使用-XX:+UseParallelOldGC(年轻代、老年代都使用并行回收收集器)或者-XX:+UseParallelGC(年轻代使用并行收集器，老年代使用串行回收收集器)运行输出的。
* Garbage-First heap: 是使用-XX:+UseG1GC(G1收集器)运行输出的。  

&emsp;&emsp;一般来说，目前只在嵌入式应用场景下才使用Serial GC.








