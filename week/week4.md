

### Algorithm

```
class Solution {
    public int lengthOfLongestSubstring(String s) {

        if(s.equals("")){
            return 0;
        }

        if(s.length() == 1){
            return 1;
        }
        int maxLength = 1;

        //窗口滑动算法
        char[] chars = s.toCharArray();
        Deque<Character> deque = new LinkedList<>();

        for (int i = 0; i < chars.length; i++) {
            Character current = new Character(chars[i]);

            while(deque.contains(current)){
                deque.removeFirst();
            }
            deque.addLast(current);

            maxLength = maxLength > deque.size() ? maxLength:deque.size(); 

        }


        return maxLength;

    }
}
```

### Review

[原文链接](https://blog.learngoprogramming.com/a-visual-guide-to-golang-memory-allocator-from-ground-up-e132258453ed)
##### 物理内存和虚拟内存
操作系统通过内存分配器管理虚拟内存空间
Address line 连接电容，充电是1，不充电是0，当cpu读取数据时，有充电的表示1，没充电表示0
每个byte都有个内存地址，因此系统内存是有限的，因此每个处理器都运行在自己的沙盒内
把虚拟地址和物理地址之间的映射叫MMU，地址太多，因此有了分页的概念。，大部分是4k.虚拟内存不存储，他是虚拟地址和物理地址的映射关系。系统开分配内存的时候使用 brk（sbrk/mmap）等系统命令。
Memory Allocator
除了改变brk的地址，他还负责减少内存碎片和快速分配内存。

##### TCMalloc
最接近golang内存分配的算法
核心思想是把内存分为不同大小的级别，这样减少锁的粒度。包含两部分thread memory and page heap
thread memory
每个线程都包含可以自由分配的内存块，这些时固定大小的，可以减少锁的使用。分配的内存小于32k时，可以直接从缓存读取，非常高效。
page heap
堆内存包含一些页的集合，每个集合叫做一个span，每个span包含起始页的地址和页数。申请的内存大于32k时开始使用这个span。当堆的内存也不够的时候，page heap会向系统申请内存。golang最初是基于tcmalloc，但是已经变了很多。
##### Go Memory Allocator
go 内存管理器将内存分配为67个不同大小的内存类。go也有页的概念，每页有8kb，分成了8块，每块1kb。这个管理结构叫mspan.
##### mspan
他是个双链表结构。每个item包含页的起始地址，页数和span类
##### mcache
go的每一个处理器都有个mcache,当处理器需要内存时直接获取。一个处理器最多有一个goroutine在运行。它包含mspan作为缓存。
如果申请的内存小于32k时直接找到合适大小的mspan使用。当mcache内存不足时，会从mcentral获取。
##### mcentral
mcentral搜集给定大小的类，包含两种mspan
包含两种
1、空mspan列表
2、非空列表
申请的时候，会从非空列表拿出，放到空的列表。释放后会根据其中可用对象的数量放在非空列中
mcentral被维护在mheap中
##### mheap
他是堆管理对象。全局一个。他负责虚拟地址空间。mheap有一个mcentral的列表
大于32k的直接从堆申请
##### 申请流程
大于32k直接从堆申请
小于16b,mcachetiny 分配器申请
16B ~ 32k mcache计算大小，用block分配器申请
mcache内存不够会向mcenterl申请，mcenterl不够会从mheap申请，直到操作系统。总结来看 go是通过不同的内存大小分配不同的内存策略。






### Tips
https://www.jianshu.com/p/7e98b8ced896

### Share
##### 记录一次goroutine泄露排查过程

我们有个 golang项目，上线后，goroutine的数量一直在涨，而且没有回收的记录。很明显是有goroutine泄露。那么在哪里泄露的呢？
![895db0fcc04c048d210f01d2515de671.jpeg](evernotecid://F6BD601A-885F-4031-B26C-5478BF9DBFDC/appyinxiangcom/11738527/ENResource/p1348)


###### 1、排查log
从图片可以看出每次goroutine增加都有个明显的时间点。根据这个找到对应的日志，发现这个时候都会发送一条kakfa消息。定位到了大体的代码位置。

###### 2、线下复现
排除掉其他操作，仅仅发送kafka消息，观察goroutine的数量。监控使用的是pprof这个工具，这个不熟悉的可以自己google查下。
```
package main
import (    
    "fmt"    "net/http"    
    _ "net/http/pprof"
)
func main() {    
    // 开启pprof，监听请求    
    ip := "0.0.0.0:6060"    
    if err := http.ListenAndServe(ip, nil); err != nil {        fmt.Printf("start pprof failed on %s\n", ip)    
    }
}
```
每次发送都发下goroutine的数量在涨，因此业务代码这块是有问题的。

###### 3、定位代码

http://ip:port/debug/pprof/goroutine?debug=1
查看生成的routine
![d243fbc5c2c6fdbfe75eb74f8d81d263.jpeg](evernotecid://F6BD601A-885F-4031-B26C-5478BF9DBFDC/appyinxiangcom/11738527/ENResource/p1349)
左上角的18是每次点击发送都会增加的数字。

那么这些routine存在多久呢
http://ip:port/debug/pprof/goroutine?debug=2
![0aa4143e255077e14cffd276dffac5d9.jpeg](evernotecid://F6BD601A-885F-4031-B26C-5478BF9DBFDC/appyinxiangcom/11738527/ENResource/p1350)
左上角是routine的id和存在的时间，然后下面是具体的代码行数。

这样就定位到了routine泄露的位置。
