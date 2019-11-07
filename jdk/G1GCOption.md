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
- [G1GCOption](#g1gcoption)
  - [1、-XX:+PrintGCDetails](#1-xxprintgcdetails)
  - [2、-Xloggc](#2-xloggc)
  - [3、-XX:+UseSerialGC](#3-xxuseserialgc)
  - [4、-XX:+UseParNewGC](#4-xxuseparnewgc)
  - [5、-XX:+UseParallelGC](#5-xxuseparallelgc)
  - [6、-XX:+UseParallelOldGC](#6-xxuseparalleloldgc)
  - [7、-XX:+UseConcMarkSweepGC](#7-xxuseconcmarksweepgc)
  - [8、-XX:+UseG1GC](#8-xxuseg1gc)
  - [9、-XX:+PrintGCApplicationStoppedTime](#9-xxprintgcapplicationstoppedtime)
  - [10、-XX:ConcGCThreads](#10-xxconcgcthreads)
  - [11、-XX:G1HeapRegionSize](#11-xxg1heapregionsize)
  - [12、-XX:G1HeapWastePercent](#12-xxg1heapwastepercent)
  - [13、-XX:G1MixedGCCountTarget](#13-xxg1mixedgccounttarget)
  - [14、-XX:+G1PrintRegionLivenessInfo](#14-xxg1printregionlivenessinfo)
  - [15、-XX:G1ReservePercent](#15-xxg1reservepercent)
  - [16、-XX:+G1SummarizeRSetStats](#16-xxg1summarizersetstats)
  
&nbsp;&nbsp;
&nbsp;&nbsp;
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
&emsp;&emsp;输出日志声明了使用-XX:+UseSerialGC选项，其它选项没有变化，对应的输出由Parallel GC的“PSYoungGen”变化成了“def new generation”，这个主要是由于收集器的不同而造成的，来看一下规律。  
* 串行收集器：即输出 DefNew，是使用-XX:+UseSerialGC（年轻代、老年代都用串行回收收集器）运行后输出的。
* 并行收集器：即输出 ParNew，是使用-XX:+UseParNewGC(年轻代使用并行收集器，老年代用串行回收收集器) 或者-XX:+UseConcMarkSweepGC(年轻代使用并行收集器，老年代使用CMS)运行后输出的。
* PSYoungGen：是使用-XX:+UseParallelOldGC(年轻代、老年代都使用并行回收收集器)或者-XX:+UseParallelGC(年轻代使用并行收集器，老年代使用串行回收收集器)运行输出的。
* Garbage-First heap: 是使用-XX:+UseG1GC(G1收集器)运行输出的。  

&emsp;&emsp;一般来说，目前只在嵌入式应用场景下才使用Serial GC.  
&nbsp;&nbsp;
&nbsp;&nbsp;

## 4、-XX:+UseParNewGC
&emsp;&emsp;ParNew GC是一种独占式GC，和Serial GC的区别是多线程GC执行。  
&emsp;&emsp;在JVM的运行选项里添加-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log -XX:+UseParNewGC
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(8951836k free), swap 19143876k(9536836k free)
CommandLine flags: -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParNewGC 
Heap
 par new generation   total 78656K, used 30784K [0x00000006c1e00000, 0x00000006c7350000, 0x0000000716950000)
  eden space 69952K,  44% used [0x00000006c1e00000, 0x00000006c3c10260, 0x00000006c6250000)
  from space 8704K,   0% used [0x00000006c6250000, 0x00000006c6250000, 0x00000006c6ad0000)
  to   space 8704K,   0% used [0x00000006c6ad0000, 0x00000006c6ad0000, 0x00000006c7350000)
 tenured generation   total 174784K, used 0K [0x0000000716950000, 0x0000000721400000, 0x00000007c0000000)
   the space 174784K,   0% used [0x0000000716950000, 0x0000000716950000, 0x0000000716950200, 0x0000000721400000)
 Metaspace       used 8659K, capacity 8954K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
```
正确的输出了“par new generation”。除了正常的GC日志输出以外，在运行时还会看到打印输出警告信息。
```shell
Java HotSpot(TM) 64-Bit Server VM warning: Using the ParNew young collector with the Serial old collector is deprecated and will likely be removed in a future release
```
这段话说明未来ParNew垃圾回收器不能再用了。  
&nbsp;&nbsp;
&nbsp;&nbsp;

## 5、-XX:+UseParallelGC
&emsp;&emsp;这一就是1里默认不选择GC时的输出，VM选项为：-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log -XX:+UseParallelGC  
&emsp;&emsp;Parallel GC是JDK7之后的默认GC，它通过多线程的方式减缓了独占式GC的副作用（停顿时间比较长）。年轻代和老年代在Parallel GC里都是并行执行并且是独占的，老年代也执行了压缩操作，这个压缩操作指的是移动存活对象到相邻位置，这样可以减少内存浪费，更好的实现内存布局。  
&emsp;&emsp;Serial GC使用单一线程执行GC，而Parallel GC则使用多个线程并发执行，因此Parallel GC较Serial GC具有更快的回收速度。Parallel GC适用于多核CPU且使用了较大内存空间的场景。  
&emsp;&emsp;此外，Parallel GC又被称为“高吞吐GC（Throughout GC）”。  
&nbsp;&nbsp;
&nbsp;&nbsp;

## 6、-XX:+UseParallelOldGC
&emsp;&emsp;Parallel Old GC在JDK5被引入，与Parallel GC相比，唯一的区别在于Parallel Old GC算法是为老年代设计的。它的执行过程分为三步，即标记（Mark）、总结（Summary）、压缩（Compaction）。其中Summary步骤为存活的对象在已执行过GC的空间上标出位置，因此与Mark-Sweep-Compact算法中的Sweep步骤有所区别，并需要一些复杂步骤才能完成。  
&emsp;&emsp;在JDK7U45之前，我们在使用Parallel GC时是区分年轻代和老年代的，即老年代并行回收收集器需要通过设置UseParallelOldGC来启动。在JDK7U45之后两者合并了。  
&nbsp;&nbsp;
&nbsp;&nbsp;

## 7、-XX:+UseConcMarkSweepGC
&emsp;&emsp;并发标记-清理（ Concurrent Mark-Sweep）GC相比其它GC都要复杂。初始标记（Initial Mark）比较简单，只有靠近类加载器的存活对象会被标记，因此停顿时间（Stop-the-world pause）比较短暂。在并发标记（Concurrent Mark）阶段，刚被确认和标记过的存活对象所关联的对象将会被跟踪和检测存活状态。此步骤的不同之处在于有多个线程并行处理此过程。在重标记（Remark）阶段，由并发标记所关联的新增或中止的对象会被检测。在最后的并发清理（Concurrent Sweep）阶段，垃圾回收过程被真正地执行。在垃圾回收执行过程中，其他线程依然可以保持并行执行。受益于CMS GC的执行方式，在GC执行期间系统中断的时间非常短暂（G1 GC 也采纳了这种设计文案）。此外，CMS GC也被称为低延迟GC，适用于所有应用对响应时间要求比较严格的场景。  
&emsp;&emsp;在JVM的运行选项里添加-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log -XX:+UseConcMarkSweepGC
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(9031864k free), swap 19143876k(8089536k free)
CommandLine flags: -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:MaxNewSize=872415232 -XX:MaxTenuringThreshold=6 -XX:OldPLABSize=16 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseConcMarkSweepGC -XX:-UseLargePagesIndividualAllocation -XX:+UseParNewGC 
Heap
 par new generation   total 78656K, used 30783K [0x00000006c1e00000, 0x00000006c7350000, 0x00000006f5e00000)
  eden space 69952K,  44% used [0x00000006c1e00000, 0x00000006c3c0fdb8, 0x00000006c6250000)
  from space 8704K,   0% used [0x00000006c6250000, 0x00000006c6250000, 0x00000006c6ad0000)
  to   space 8704K,   0% used [0x00000006c6ad0000, 0x00000006c6ad0000, 0x00000006c7350000)
 concurrent mark-sweep generation total 174784K, used 0K [0x00000006f5e00000, 0x00000007008b0000, 0x00000007c0000000)
 Metaspace       used 8666K, capacity 8950K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
```
&emsp;&emsp;在使用CMS GC之前需要对系统做全面的分析。另外，为了避免过多的内存碎片而需要执行压缩任务时，CMS GC会比任何其他GC带来更多的Stop-the-world时间，所以需要分析和判断压缩任务执行的频率及其耗时情况。  
&nbsp;&nbsp;
&nbsp;&nbsp;

## 8、-XX:+UseG1GC
&emsp;&emsp;使用UseG1GC这个选项要求JDK7或JDK8的对应的JVM采用G1 GC。  
&emsp;&emsp;在JVM的运行选项里添加-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log -XX:+UseG1GC
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(8938604k free), swap 19143876k(8349288k free)
CommandLine flags: -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
Heap
 garbage-first heap   total 262144K, used 11264K [0x00000006c1e00000, 0x00000006c1f00800, 0x00000007c0000000)
  region size 1024K, 12 young (12288K), 0 survivors (0K)
 Metaspace       used 8658K, capacity 8954K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K

```
&emsp;&emsp;G1 GC的日志输出和其它GC有所不同，它更加简洁。这里没有进入到一个评估阶段，评估阶段就是确认有多少对象需要被回收，通常是针对年轻代或者年轻代+老年代的。从上面的输出我们可以看出一共有256MB（262144/1024）的堆内存空间，其中使用了11MB。Region每个是1MB，有12个年轻代Region。元数据空间的使用情况也做了相应的介绍，使用了8.5MB，可用的为8.7MB。  
&nbsp;&nbsp;
&nbsp;&nbsp;

## 9、-XX:+PrintGCApplicationStoppedTime
&emsp;&emsp;如果使用该选项，会输出GC造成应用程序暂停的时间。一般和-XX:+PrintGCApplicationConcurrentTime组合起来一起使用，这样比较有利于查看输出。  
&emsp;&emsp;在JVM的运行选项里添加-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log -XX:+UseG1GC
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(8662324k free), swap 19143876k(8269020k free)
CommandLine flags: -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
1.027: Application time: 0.9688679 seconds
1.029: Total time for which application threads were stopped: 0.0000442 seconds, Stopping threads took: 0.0000147 seconds
2.729: Application time: 1.6993237 seconds
2.729: Total time for which application threads were stopped: 0.0000989 seconds, Stopping threads took: 0.0000112 seconds
2.730: Application time: 0.0014230 seconds
2.730: Total time for which application threads were stopped: 0.0000422 seconds, Stopping threads took: 0.0000071 seconds
2.733: Application time: 0.0023802 seconds
2.733: Total time for which application threads were stopped: 0.0000343 seconds, Stopping threads took: 0.0000065 seconds
3.732: Application time: 0.9996228 seconds
3.732: Total time for which application threads were stopped: 0.0001349 seconds, Stopping threads took: 0.0000425 seconds
4.129: Application time: 0.3968763 seconds
4.130: Total time for which application threads were stopped: 0.0001919 seconds, Stopping threads took: 0.0000421 seconds
5.090: Application time: 0.9604578 seconds
5.090: Total time for which application threads were stopped: 0.0000321 seconds, Stopping threads took: 0.0000146 seconds
Heap
 garbage-first heap   total 262144K, used 11264K [0x00000006c1e00000, 0x00000006c1f00800, 0x00000007c0000000)
  region size 1024K, 12 young (12288K), 0 survivors (0K)
 Metaspace       used 8666K, capacity 8950K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
5.091: Application time: 0.0006537 seconds
```
看一下这两行输出
```shell
1.027: Application time: 0.9688679 seconds
1.029: Total time for which application threads were stopped: 0.0000442 seconds, Stopping threads took: 0.0000147 seconds
```
&emsp;&emsp;这里表示应用程序执行了0.96s，GC线程造成的停顿时间大约为0.0000442s。由于程序在运行过程中进行了多次回收，所以你看到这里有多次的时间打印。如果发现某个时间很长，你就要关注代码和设计了，分析哪里可能出现了实现或设计弱点（不一点是缺陷），再根据实际情况进行优化，也许是代码逻辑复杂5造成的，也可能是代码编写时频繁创建对象造成的。  
&emsp;&emsp;这里应用程序会被暂停是由于G1 GC针对年轻代（有时候是年轻代+老年代）有一个评估阶段，这个评估阶段实质上是在做数据拷贝，既然是拷贝，就要有一个基准点，那么为了维护这个点，需要设置对应的应用程序暂停时间，这个时间段就称为保护点（safepoint）。  
&nbsp;&nbsp;
&nbsp;&nbsp;

## 10、-XX:ConcGCThreads
&emsp;&emsp;这个选项用来设置与Java应用程序线程并行执行的GC线程数量，默认为GC独占时运行线程的1/4。这个选项设置过大会导致Java应用程序可以使用的CPU资源减少，如果小一点则会对应用程序有利，但是过小就会增加GC并行循环的执行时间，反过来减少Java应用程序的运行时间（因为独占期时间拉长）。  
&emsp;&emsp;设置一个比较大的ConcGCThreads值，比如-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log -XX:+UseG1GC -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime -XX:ConcGCThreads=16，会看到JVM抛出如下用红色标记的错误，表明在初始化JVM时出错，不能够创建并行标记阶段，并且最终拒绝执行程序。这个错误表明当前机器只允许起动10个试行GC线程，这是根据运行程序所在机器的CPU的核数计算出来的。
```shell
Error occurred during initialization of VM
Could not create/initialize ConcurrentMark
Java HotSpot(TM) 64-Bit Server VM warning: Can't have more ConcGCThreads (16) than ParallelGCThreads (10).
```
&emsp;&emsp;把ConcGCThreads改为10之后，即-XX:ConcGCThreads=10，运行输出
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(9675948k free), swap 19143876k(7236064k free)
CommandLine flags: -XX:ConcGCThreads=10 -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
1.028: Application time: 0.9649023 seconds
1.028: Total time for which application threads were stopped: 0.0000464 seconds, Stopping threads took: 0.0000133 seconds
3.028: Application time: 2.0005951 seconds
3.029: Total time for which application threads were stopped: 0.0000324 seconds, Stopping threads took: 0.0000141 seconds
3.988: Application time: 0.9598846 seconds
3.989: Total time for which application threads were stopped: 0.0001156 seconds, Stopping threads took: 0.0000133 seconds
3.990: Application time: 0.0015112 seconds
3.990: Total time for which application threads were stopped: 0.0000510 seconds, Stopping threads took: 0.0000091 seconds
3.993: Application time: 0.0025014 seconds
3.993: Total time for which application threads were stopped: 0.0000532 seconds, Stopping threads took: 0.0000076 seconds
4.131: Application time: 0.1379436 seconds
4.131: Total time for which application threads were stopped: 0.0000840 seconds, Stopping threads took: 0.0000151 seconds
5.095: Application time: 0.9645026 seconds
5.095: Total time for which application threads were stopped: 0.0000670 seconds, Stopping threads took: 0.0000508 seconds
Heap
 garbage-first heap   total 262144K, used 11264K [0x00000006c1e00000, 0x00000006c1f00800, 0x00000007c0000000)
  region size 1024K, 12 young (12288K), 0 survivors (0K)
 Metaspace       used 8665K, capacity 8954K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
5.096: Application time: 0.0010852 seconds
```
&emsp;&emsp;如果不设置并行标记线程的数量，默认情况下是用这个公式计算出来的：
```
ConcGCThreads=Max((ParallelGCThreads#+2)/4, 1)
```
&nbsp;&nbsp;
&nbsp;&nbsp;

## 11、-XX:G1HeapRegionSize
&emsp;&emsp;这是G1独有的选项，它是专门针对Region这个概念的对应设置选项，后续GC应该会继续采用Region这个概念。Region的大小默认为堆大小的1/2000，也可以设置为1MB、2MB、4MB、8MB、16MB以及32MB，这六个划分档次。  
&emsp;&emsp;增大Region块的大小有利于处理大对象。反之，如果Region大小设置过小，则会降低G1的灵活性，对各个年龄代的大小都会造成分配问题。  
&emsp;&emsp;在JVM的运行选项里添加-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log  -XX:+UseG1GC -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime -XX:G1HeapRegionSize=32M
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(9503920k free), swap 19143876k(7407324k free)
CommandLine flags: -XX:G1HeapRegionSize=33554432 -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
1.023: Application time: 0.9650295 seconds
1.023: Total time for which application threads were stopped: 0.0000472 seconds, Stopping threads took: 0.0000137 seconds
1.287: Application time: 0.2635353 seconds
1.287: Total time for which application threads were stopped: 0.0000919 seconds, Stopping threads took: 0.0000112 seconds
1.288: Application time: 0.0014336 seconds
1.288: Total time for which application threads were stopped: 0.0000448 seconds, Stopping threads took: 0.0000071 seconds
1.290: Application time: 0.0023294 seconds
1.290: Total time for which application threads were stopped: 0.0000516 seconds, Stopping threads took: 0.0000100 seconds
2.291: Application time: 1.0001051 seconds
2.291: Total time for which application threads were stopped: 0.0001680 seconds, Stopping threads took: 0.0000585 seconds
3.291: Application time: 1.0000774 seconds
3.291: Total time for which application threads were stopped: 0.0000413 seconds, Stopping threads took: 0.0000162 seconds
4.126: Application time: 0.8348864 seconds
4.126: Total time for which application threads were stopped: 0.0004491 seconds, Stopping threads took: 0.0000945 seconds
5.088: Application time: 0.9617559 seconds
5.088: Total time for which application threads were stopped: 0.0000416 seconds, Stopping threads took: 0.0000172 seconds
Heap
 garbage-first heap   total 262144K, used 0K [0x00000006c0000000, 0x00000006c2000040, 0x00000007c0000000)
  region size 32768K, 1 young (32768K), 0 survivors (0K)
 Metaspace       used 8661K, capacity 8950K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
5.089: Application time: 0.0008841 seconds
```
&nbsp;&nbsp;
&nbsp;&nbsp;

## 12、-XX:G1HeapWastePercent
&emsp;&emsp;这个选项控制G1 GC不回收的空闲内存比例，默认是堆内存的5%，G1 GC在回收过程中会回收所有Region的内存，并持续地做这个工作直到空闲内存比例达到设置的这个值为止，所以对于设置了较大值的堆内存来说，需要要采用比较低的比例，这样可以确保较小部分的内存不被回收。  
&emsp;&emsp;设置不回收比例为99%，在JVM的运行选项里添加:-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log  -XX:+UseG1GC -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime -XX:G1HeapRegionSize=2M -XX:G1HeapWastePercent=99
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(9199344k free), swap 19143876k(7357736k free)
CommandLine flags: -XX:G1HeapRegionSize=2097152 -XX:G1HeapWastePercent=99 -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
0.527: Application time: 0.4656978 seconds
0.527: Total time for which application threads were stopped: 0.0001081 seconds, Stopping threads took: 0.0000108 seconds
0.528: Application time: 0.0013867 seconds
0.528: Total time for which application threads were stopped: 0.0000346 seconds, Stopping threads took: 0.0000057 seconds
0.531: Application time: 0.0023573 seconds
0.531: Total time for which application threads were stopped: 0.0000346 seconds, Stopping threads took: 0.0000063 seconds
1.530: Application time: 0.9995402 seconds
1.530: Total time for which application threads were stopped: 0.0000526 seconds, Stopping threads took: 0.0000128 seconds
3.531: Application time: 2.0005495 seconds
3.531: Total time for which application threads were stopped: 0.0001111 seconds, Stopping threads took: 0.0000429 seconds
4.130: Application time: 0.5983626 seconds
4.130: Total time for which application threads were stopped: 0.0000776 seconds, Stopping threads took: 0.0000169 seconds
5.096: Application time: 0.9665322 seconds
5.096: Total time for which application threads were stopped: 0.0000282 seconds, Stopping threads took: 0.0000128 seconds
Heap
 garbage-first heap   total 262144K, used 12288K [0x00000006c1e00000, 0x00000006c2000400, 0x00000007c0000000)
  region size 2048K, 7 young (14336K), 0 survivors (0K)
 Metaspace       used 8650K, capacity 8946K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
5.097: Application time: 0.0009225 seconds
```
&emsp;&emsp;另一个极端，设置为1%，在JVM的运行选项里添加:-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log  -XX:+UseG1GC -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime -XX:G1HeapRegionSize=2M -XX:G1HeapWastePercent=1
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(9168472k free), swap 19143876k(7351944k free)
CommandLine flags: -XX:G1HeapRegionSize=2097152 -XX:G1HeapWastePercent=1 -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
1.026: Application time: 0.9649561 seconds
1.026: Total time for which application threads were stopped: 0.0000481 seconds, Stopping threads took: 0.0000135 seconds
1.937: Application time: 0.9109309 seconds
1.937: Total time for which application threads were stopped: 0.0001107 seconds, Stopping threads took: 0.0000130 seconds
1.938: Application time: 0.0014136 seconds
1.938: Total time for which application threads were stopped: 0.0000497 seconds, Stopping threads took: 0.0000067 seconds
1.941: Application time: 0.0024418 seconds
1.941: Total time for which application threads were stopped: 0.0000557 seconds, Stopping threads took: 0.0000091 seconds
2.941: Application time: 0.9998064 seconds
2.941: Total time for which application threads were stopped: 0.0000504 seconds, Stopping threads took: 0.0000181 seconds
4.127: Application time: 1.1868105 seconds
4.127: Total time for which application threads were stopped: 0.0000645 seconds, Stopping threads took: 0.0000141 seconds
5.093: Application time: 0.9657092 seconds
5.093: Total time for which application threads were stopped: 0.0000352 seconds, Stopping threads took: 0.0000137 seconds
Heap
 garbage-first heap   total 262144K, used 12288K [0x00000006c1e00000, 0x00000006c2000400, 0x00000007c0000000)
  region size 2048K, 7 young (14336K), 0 survivors (0K)
 Metaspace       used 8668K, capacity 8946K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
5.094: Application time: 0.0009615 seconds
```
&emsp;&emsp;这两个设置都是比较极端的，一般情况下我们不会做出调整，即采用5%的默认值。当设置为1%时，本例的差别不是很明显，但是GC线程造成的应用程序暂停时间已经比99%有了明显的增长。  
&nbsp;&nbsp;
&nbsp;&nbsp;

## 13、-XX:G1MixedGCCountTarget
&emsp;&emsp;老年代Region的回收时间通常来说比年轻代Region稍长一些，这个选项可以设置一个并行循环之后启动多少个混合GC，默认值是8个。设置一个比较大的值可以让G1 GC在老年代Region回收时多花一些时间，如果一个混合GC的停顿时间很长，说明它要做的事情很多，所以可以增大这个值的设置，但是如果这个值过大，也会造成并行循环等待混合GC完成的时间相应的增加。  
&emsp;&emsp;在JVM的运行选项里添加:-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log  -XX:+UseG1GC -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime -XX:G1HeapRegionSize=2M -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=80
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(8581256k free), swap 19143876k(7207080k free)
CommandLine flags: -XX:G1HeapRegionSize=2097152 -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=80 -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
1.025: Application time: 0.9649554 seconds
1.025: Total time for which application threads were stopped: 0.0000471 seconds, Stopping threads took: 0.0000130 seconds
3.642: Application time: 2.6170361 seconds
3.642: Total time for which application threads were stopped: 0.0001221 seconds, Stopping threads took: 0.0000115 seconds
3.644: Application time: 0.0014123 seconds
3.644: Total time for which application threads were stopped: 0.0000421 seconds, Stopping threads took: 0.0000054 seconds
3.646: Application time: 0.0023982 seconds
3.646: Total time for which application threads were stopped: 0.0000366 seconds, Stopping threads took: 0.0000063 seconds
4.128: Application time: 0.4824259 seconds
4.129: Total time for which application threads were stopped: 0.0000943 seconds, Stopping threads took: 0.0000143 seconds
5.091: Application time: 0.9628365 seconds
5.091: Total time for which application threads were stopped: 0.0000308 seconds, Stopping threads took: 0.0000137 seconds
Heap
 garbage-first heap   total 262144K, used 12288K [0x00000006c1e00000, 0x00000006c2000400, 0x00000007c0000000)
  region size 2048K, 7 young (14336K), 0 survivors (0K)
 Metaspace       used 8662K, capacity 8958K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
5.092: Application time: 0.0006737 seconds
```
&emsp;&emsp;这个选项和G1MixedGCLiveThresholdPercent选项（默认值为65%，指的是混合回收阶段回收老年代区间的上限阀值）有直接关系，G1MixedGCLiveThresholdPercent设置完毕后，对存活数据的回收上限为G1MixedGCLiveThresholdPercent的旧区域执行混合垃圾回收的目标次数，这个选项会自动调整为满足配置的阀值，以便能够快速回收对象。  
&nbsp;&nbsp;
&nbsp;&nbsp;

## 14、-XX:+G1PrintRegionLivenessInfo
&emsp;&emsp;开启这个选项会在标记循环阶段完成之后输出详细信息，专业一点的叫法是诊断选项，所以在使用之前需要开启选项UnlockDiagnosticVMOptions。这个选项启用后会打印堆内存内部每个Region里面的存活对象信息，这些信息包括使用率、RSet大小、回收一个Region的价值。（Region内容回收价值评估，即性价比）。  
&emsp;&emsp;这个选项输出的信息对于调试堆内Region是很有效的，不过对于一个很大的堆内存来说，由于每个Region信息都输出了，所以信息量也是挺大的。  
&emsp;&emsp;在JVM的运行选项里添加:-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log  -XX:+UseG1GC -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime -XX:G1HeapRegionSize=2M -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=10 -XX:+G1PrintRegionLivenessInfo -XX:+UnlockDiagnosticVMOptions  
直接运行发现抛错误：
```shell
Error: VM option 'G1PrintRegionLivenessInfo' is diagnostic and must be enabled via -XX:+UnlockDiagnosticVMOptions.
```
&emsp;&emsp;注意：-XX:+UnlockDiagnosticVMOptions必须放在-XX:+G1PrintRegionLivenessInfo的前面，证明VM的选项执行也是串行的，这就是为什么抛出了上面的错误。  
&emsp;&emsp;在JVM的运行选项里添加:-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log  -XX:+UseG1GC -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime -XX:G1HeapRegionSize=2M -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=10 -XX:+UnlockDiagnosticVMOptions -XX:+G1PrintRegionLivenessInfo
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(8304752k free), swap 19143876k(6738464k free)
CommandLine flags: -XX:G1HeapRegionSize=2097152 -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=10 -XX:+G1PrintRegionLivenessInfo -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UnlockDiagnosticVMOptions -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
1.025: Application time: 0.9615710 seconds
1.025: Total time for which application threads were stopped: 0.0000474 seconds, Stopping threads took: 0.0000128 seconds
1.495: Application time: 0.4691993 seconds
1.495: Total time for which application threads were stopped: 0.0001211 seconds, Stopping threads took: 0.0000109 seconds
1.496: Application time: 0.0016289 seconds
1.496: Total time for which application threads were stopped: 0.0000797 seconds, Stopping threads took: 0.0000090 seconds
1.500: Application time: 0.0033770 seconds
1.500: Total time for which application threads were stopped: 0.0000775 seconds, Stopping threads took: 0.0000114 seconds
2.499: Application time: 0.9994881 seconds
2.499: Total time for which application threads were stopped: 0.0000426 seconds, Stopping threads took: 0.0000175 seconds
3.500: Application time: 1.0002822 seconds
3.500: Total time for which application threads were stopped: 0.0000416 seconds, Stopping threads took: 0.0000150 seconds
4.130: Application time: 0.6302136 seconds
4.130: Total time for which application threads were stopped: 0.0000676 seconds, Stopping threads took: 0.0000144 seconds
5.101: Application time: 0.9711937 seconds
5.101: Total time for which application threads were stopped: 0.0000282 seconds, Stopping threads took: 0.0000126 seconds
Heap
 garbage-first heap   total 262144K, used 12288K [0x00000006c1e00000, 0x00000006c2000400, 0x00000007c0000000)
  region size 2048K, 7 young (14336K), 0 survivors (0K)
 Metaspace       used 8663K, capacity 8950K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
5.102: Application time: 0.0007128 seconds
```
&nbsp;&nbsp;
&nbsp;&nbsp;

## 15、-XX:G1ReservePercent
&emsp;&emsp;每个年龄代都会有一些对象可以进入下一个阶段，为了确保这个提升过程正常完成，我们允许G1 GC保留一些内存，这样就可以避免出现“to space  exhaust”错误，这个选项就是为了这个用途。  
&emsp;&emsp;这个选项默认保留堆内存的10%。注意，这个预留内存空间不能用于年轻代。  
&emsp;&emsp;对于一个拥有大内存的堆内存来说，这个值不能过大，因为它不能用于年轻代，这就意味着年轻代可用内存降低了。减小这个值有助于给年轻代留出更大的内存空间、更长的GC时间，这对提升性能吞吐量有好处。  
&emsp;&emsp;测试一个极端值，配置G1ReservePercent为100%。在JVM的运行选项里添加:-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log  -XX:+UseG1GC -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime -XX:ConcGCThreads=1 -XX:G1HeapRegionSize=2M -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=10 -XX:+UnlockDiagnosticVMOptions -XX:+G1PrintRegionLivenessInfo -XX:G1ReservePercent=100  
可以看到运行时抛出了异常：
```shell
Java HotSpot(TM) 64-Bit Server VM warning: G1ReservePercent is set to a value that is too large, it's been updated to 50
```
&emsp;&emsp;这里提示我们，最大的保留空间不能超过50%，指的是堆内存的50%。减少为50%，运行后日志输出：
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(8616644k free), swap 19143876k(5560756k free)
CommandLine flags: -XX:ConcGCThreads=1 -XX:G1HeapRegionSize=2097152 -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=10 -XX:+G1PrintRegionLivenessInfo -XX:G1ReservePercent=50 -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UnlockDiagnosticVMOptions -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
1.026: Application time: 0.9654210 seconds
1.026: Total time for which application threads were stopped: 0.0000620 seconds, Stopping threads took: 0.0000165 seconds
1.618: Application time: 0.5920360 seconds
1.619: Total time for which application threads were stopped: 0.0000971 seconds, Stopping threads took: 0.0000113 seconds
1.620: Application time: 0.0013883 seconds
1.620: Total time for which application threads were stopped: 0.0000443 seconds, Stopping threads took: 0.0000076 seconds
1.622: Application time: 0.0023738 seconds
1.622: Total time for which application threads were stopped: 0.0000362 seconds, Stopping threads took: 0.0000069 seconds
2.622: Application time: 0.9997851 seconds
2.622: Total time for which application threads were stopped: 0.0000836 seconds, Stopping threads took: 0.0000484 seconds
4.109: Application time: 1.4863118 seconds
4.109: Total time for which application threads were stopped: 0.0000684 seconds, Stopping threads took: 0.0000164 seconds
5.092: Application time: 0.9831767 seconds
5.092: Total time for which application threads were stopped: 0.0001584 seconds, Stopping threads took: 0.0000984 seconds
Heap
 garbage-first heap   total 262144K, used 10240K [0x00000006c1e00000, 0x00000006c2000400, 0x00000007c0000000)
  region size 2048K, 6 young (12288K), 0 survivors (0K)
 Metaspace       used 8658K, capacity 8950K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
5.093: Application time: 0.0012559 seconds
```
&nbsp;&nbsp;
&nbsp;&nbsp;

## 16、-XX:+G1SummarizeRSetStats
&emsp;&emsp;和G1PrintRegionLivenessInfo选项一样，这个选项也是一个诊断选项，所以也需要开启UnlockDiagnosticVMOptions选项后才能使用，这也就意味着-XX:+UnlockDiagnosticVMOptions选项需要放在-XX:G1SummarizeRSetStats选项前面。  
&emsp;&emsp;这个选项和-XX:G1SummarizeRSetStatsPeriod一起使用的时候会阶段性地打印RSets的详细信息，这有助于找到RSets里存在的问题。  
&emsp;&emsp;在JVM的运行选项里添加:-XX:+PrintGCDetails -XX:+PrintGCTimeStamps -verbose:gc -Xloggc:gc.log  -XX:+UseG1GC -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime -XX:ConcGCThreads=1 -XX:G1HeapRegionSize=2M -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=10 -XX:+UnlockDiagnosticVMOptions -XX:+G1PrintRegionLivenessInfo -XX:G1ReservePercent=10 -XX:G1SummarizeRSetStatsPeriod=10 -XX:+G1SummarizeRSetStats
```shell
Java HotSpot(TM) 64-Bit Server VM (25.202-b08) for windows-amd64 JRE (1.8.0_202-b08), built on Dec 15 2018 19:54:30 by "java_re" with MS VC++ 10.0 (VS2010)
Memory: 4k page, physical 16653508k(8397936k free), swap 19143876k(9256364k free)
CommandLine flags: -XX:ConcGCThreads=1 -XX:G1HeapRegionSize=2097152 -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=10 -XX:+G1PrintRegionLivenessInfo -XX:G1ReservePercent=10 -XX:+G1SummarizeRSetStats -XX:G1SummarizeRSetStatsPeriod=10 -XX:InitialHeapSize=266456128 -XX:MaxHeapSize=4263298048 -XX:+PrintGC -XX:+PrintGCApplicationConcurrentTime -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+UnlockDiagnosticVMOptions -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:+UseG1GC -XX:-UseLargePagesIndividualAllocation 
1.030: Application time: 0.9671489 seconds
1.033: Total time for which application threads were stopped: 0.0000483 seconds, Stopping threads took: 0.0000138 seconds
1.510: Application time: 0.4766269 seconds
1.510: Total time for which application threads were stopped: 0.0001021 seconds, Stopping threads took: 0.0000116 seconds
1.514: Application time: 0.0043000 seconds
1.514: Total time for which application threads were stopped: 0.0000488 seconds, Stopping threads took: 0.0000077 seconds
1.517: Application time: 0.0028979 seconds
1.517: Total time for which application threads were stopped: 0.0000370 seconds, Stopping threads took: 0.0000061 seconds
2.517: Application time: 1.0001329 seconds
2.517: Total time for which application threads were stopped: 0.0001303 seconds, Stopping threads took: 0.0000445 seconds
3.517: Application time: 0.9999005 seconds
3.517: Total time for which application threads were stopped: 0.0001141 seconds, Stopping threads took: 0.0000445 seconds
4.111: Application time: 0.5935367 seconds
4.111: Total time for which application threads were stopped: 0.0000644 seconds, Stopping threads took: 0.0000140 seconds
5.128: Application time: 1.0170168 seconds
5.128: Total time for which application threads were stopped: 0.0001023 seconds, Stopping threads took: 0.0000841 seconds
Heap
 garbage-first heap   total 262144K, used 10240K [0x00000006c1e00000, 0x00000006c2000400, 0x00000007c0000000)
  region size 2048K, 6 young (12288K), 0 survivors (0K)
 Metaspace       used 8654K, capacity 8950K, committed 9088K, reserved 1056768K
  class space    used 1060K, capacity 1115K, committed 1152K, reserved 1048576K
 Cumulative RS summary

 Recent concurrent refinement statistics
  Processed 0 cards
  Of 0 completed buffers:
            0 (  0.0%) by concurrent RS threads.
            0 (  0.0%) by mutator threads.
  Did 0 coarsenings.
  Concurrent RS threads times (s)
          0.00     0.00     0.00     0.00     0.00     0.00     0.00     0.00     0.00     0.00
  Concurrent sampling threads times (s)
          0.00

 Current rem set statistics
  Total per region rem sets sizes = 688K. Max = 11K.
          39K (  5.7%) by 6 Young regions
           0B (  0.0%) by 0 Humonguous regions
         649K ( 94.3%) by 122 Free regions
           0B (  0.0%) by 0 Old regions
   Static structures = 176K, free_lists = 0B.
    0 occupied cards represented.
            0 (  0.0%) entries by 6 Young regions
            0 (  0.0%) entries by 0 Humonguous regions
            0 (  0.0%) entries by 122 Free regions
            0 (  0.0%) entries by 0 Old regions
    Region with largest rem set = 127:(E)[0x00000006d1c00000,0x00000006d1e00000,0x00000006d1e00000], size = 11K, occupied = 0B.
  Total heap region code root sets sizes = 9800B.  Max = 6688B.
        7848B ( 80.1%) by 6 Young regions
           0B (  0.0%) by 0 Humonguous regions
        1952B ( 19.9%) by 122 Free regions
           0B (  0.0%) by 0 Old regions
    124 code roots represented.
          124 (100.0%) elements by 6 Young regions
            0 (  0.0%) elements by 0 Humonguous regions
            0 (  0.0%) elements by 122 Free regions
            0 (  0.0%) elements by 0 Old regions
    Region with largest amount of code roots = 127:(E)[0x00000006d1c00000,0x00000006d1e00000,0x00000006d1e00000], size = 6688B, num_elems = 105.
5.130: Application time: 0.0016779 seconds

```
