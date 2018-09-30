# RSA premaster secret error / Cannot find any provider supporting RSA/ECB/PKCS1Padding    
jdk解密出现Cannot find any provider supporting RSA/ECB/PKCS1Padding错误     
查了很多资料都不对，最后看到参考文档，测试后发现是eclipse没有用系统设置的jdk目录，而是用了eclipse自己的jdk目录。    
测试方法：    
使用命令“java -showversion -XX:+PrintVMOptions -XX:+PrintCommandLineFlags CipherTest”从命令行运行以下程序，可以正常执行，在eclipse中执行抛出异常。        
```java
import java.util.Arrays;
import java.util.TreeMap;

public class CipherTest {
    
    // run 'java -showversion -XX:+PrintVMOptions -XX:+PrintCommandLineFlags CipherTest'
    public static void main(String[] args) throws Exception {
        System.out.println(new TreeMap(System.getProperties()));
        System.out.println();
        System.out.println(Arrays.asList(java.security.Security.getProviders()));
        System.out.println();
        System.out.println(Class.forName("javax.crypto.Cipher"));
        System.out.println();
        System.out.println(javax.crypto.Cipher.getInstance("RSA/ECB/PKCS1Padding"));
        System.out.println("Test OK");
    }
}
```    
命令行执行结果：    
```cmd
VM option '+PrintVMOptions'
VM option '+PrintCommandLineFlags'
-XX:InitialHeapSize=266846080 -XX:MaxHeapSize=4269537280 -XX:+PrintCommandLineFlags -XX:+PrintVMOptions -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
java version "1.8.0_181"
Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)

{awt.toolkit=sun.awt.windows.WToolkit, file.encoding=GBK, file.encoding.pkg=sun.io, file.separator=\, java.awt.graphicsenv=sun.awt.Win32GraphicsEnvironment, java.awt.printerjob=sun.awt.windows.WPrinterJob, java.class.path=., java.class.version=52.0, java.endorsed.dirs=C:\Program Files\Java\jre1.8.0_181\lib\endorsed, java.ext.dirs=C:\Program Files\Java\jre1.8.0_181\lib\ext;C:\WINDOWS\Sun\Java\lib\ext, java.home=C:\Program Files\Java\jre1.8.0_181, java.io.tmpdir=C:\Users\MEIPIN~1\AppData\Local\Temp\, java.library.path=C:\Program Files (x86)\Common Files\Oracle\Java\javapath;C:\WINDOWS\Sun\Java\bin;C:\WINDOWS\system32;C:\WINDOWS;C:\Program Files\VanDyke Software\Clients\;C:\Program Files (x86)\Common Files\Oracle\Java\javapath;C:\Program Files (x86)\Intel\iCLS Client\;C:\Program Files\Intel\iCLS Client\;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Program Files (x86)\Intel\Intel(R) Management Engine Components\DAL;C:\Program Files\Intel\Intel(R) Management Engine Components\DAL;C:\Program Files (x86)\Intel\Intel(R) Management Engine Components\IPT;C:\Program Files\Intel\Intel(R) Management Engine Components\IPT;C:\Program Files (x86)\NVIDIA Corporation\PhysX\Common;C:\Program Files\Java\jdk1.8.0_181\bin;C:\Program Files\Git\cmd;C:\Program Files\Git\bin;C:\Program Files\EmEditor;C:\WINDOWS\system32;C:\WINDOWS;C:\WINDOWS\System32\Wbem;C:\WINDOWS\System32\WindowsPowerShell\v1.0\;C:\WINDOWS\System32\OpenSSH\;D:\apache\apache-maven-3.5.4\bin;D:\soft\mysql-5.6.41-winx64-install\bin;C:\Program Files\TortoiseGit\bin;C:\Users\meipingmi\AppData\Local\Microsoft\WindowsApps;;., java.runtime.name=Java(TM) SE Runtime Environment, java.runtime.version=1.8.0_181-b13, java.specification.name=Java Platform API Specification, java.specification.vendor=Oracle Corporation, java.specification.version=1.8, java.vendor=Oracle Corporation, java.vendor.url=http://java.oracle.com/, java.vendor.url.bug=http://bugreport.sun.com/bugreport/, java.version=1.8.0_181, java.vm.info=mixed mode, java.vm.name=Java HotSpot(TM) 64-Bit Server VM, java.vm.specification.name=Java Virtual Machine Specification, java.vm.specification.vendor=Oracle Corporation, java.vm.specification.version=1.8, java.vm.vendor=Oracle Corporation, java.vm.version=25.181-b13, line.separator=
, os.arch=amd64, os.name=Windows 10, os.version=10.0, path.separator=;, sun.arch.data.model=64, sun.boot.class.path=C:\Program Files\Java\jre1.8.0_181\lib\resources.jar;C:\Program Files\Java\jre1.8.0_181\lib\rt.jar;C:\Program Files\Java\jre1.8.0_181\lib\sunrsasign.jar;C:\Program Files\Java\jre1.8.0_181\lib\jsse.jar;C:\Program Files\Java\jre1.8.0_181\lib\jce.jar;C:\Program Files\Java\jre1.8.0_181\lib\charsets.jar;C:\Program Files\Java\jre1.8.0_181\lib\jfr.jar;C:\Program Files\Java\jre1.8.0_181\classes, sun.boot.library.path=C:\Program Files\Java\jre1.8.0_181\bin, sun.cpu.endian=little, sun.cpu.isalist=amd64, sun.desktop=windows, sun.io.unicode.encoding=UnicodeLittle, sun.java.command=CipherTest, sun.java.launcher=SUN_STANDARD, sun.jnu.encoding=GBK, sun.management.compiler=HotSpot 64-Bit Tiered Compilers, sun.os.patch.level=, sun.stderr.encoding=ms936, sun.stdout.encoding=ms936, user.country=CN, user.dir=D:\java\eclipse\eclipse-workspace\Test\bin, user.home=C:\Users\meipingmi, user.language=zh, user.name=meipingmi, user.script=, user.timezone=, user.variant=}

[SUN version 1.8, SunRsaSign version 1.8, SunEC version 1.8, SunJSSE version 1.8, SunJCE version 1.8, SunJGSS version 1.8, SunSASL version 1.8, XMLDSig version 1.8, SunPCSC version 1.8, SunMSCAPI version 1.8]

class javax.crypto.Cipher

javax.crypto.Cipher@387c703b
Test OK
```




---
## 参考文档：
[JDK-6382135 : RSA premaster secret error / Cannot find any provider supporting RSA/ECB/PKCS1Padding](https://bugs.java.com/bugdatabase/view_bug.do?bug_id=6382135)
