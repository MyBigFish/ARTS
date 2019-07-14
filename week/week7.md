### Algorithm

atoi
```
import java.util.*;

/*
 * @lc app=leetcode id=8 lang=java
 *
 * [8] String to Integer (atoi)
 */
class Solution {
    public int myAtoi(String str) {
        str = str.trim();

        if(str.length() == 0){
            return 0;
        }
        int res = 0;
    
        int i = 1;
        Map<Character,Integer> numMap = new HashMap<Character,Integer>();
        for (Integer n = 0; n < 10; n++) {
            Character c = new Character((n + "").charAt(0));
            numMap.put(c, n);
        }
    
        for (int j = 0; j < str.length(); j++) {
            char c = str.charAt(j);

             //开头为+或-
            if(c == '-' && j == 0){
                i = -1;
                continue;
            }
            if(c == '+' && j == 0){
                continue;
            }
            if(j == 0 && !Character.isDigit(c)){
                return 0;
            }

            if(!Character.isDigit(c) && c == '.'){
                return res * i;
            }

            if(!Character.isDigit(c) && res == 0){
                return 0;
            }

            if(!Character.isDigit(c)){
                return res * i;
            }
          
            Character cr = new Character(c);
            int num = numMap.get(cr);
            //判断是否超过边界
            int oneInTen = (Integer.MAX_VALUE - num) / 10;
            int mode =  Integer.MAX_VALUE % 10;
            //>
            if(res > oneInTen){
                return i * Integer.MAX_VALUE + (i == -1 ? -1:0);
            }

            if(res == oneInTen && num > mode){
                return i * Integer.MAX_VALUE + (i == -1 ? -1:0);
            }
            res = res * 10 + numMap.get(cr);
        }
        return res * i;
    }
}

```

### Review
深入了解channel
https://codeburst.io/diving-deep-into-the-golang-channels-549fd4ed21a8?gi=1d01816d6432

go使用csp的并发模型。他是一种设计模式，传输数据用channel不要用内存。

go负责管理channel，当发送或者等待接收数据会block

hchan struct
 - datasize 初始化的大小
 - elemszie 每个元素的大小
 - buf 是一个循环队列，用于存储数据，buffer channel使用
 - closed 标记是否关闭，创建的时候是0，1表示关闭
 - sendx和recvx标记循环表的状态，支持发送和接收数据
 - recvq和sendq是等待队列。当发送或接收数据时，这个记录阻塞的协程
 - lock 保证协程读写数据原子性
 
sudog struct
sudog表示goroutine
当数据还没接收到，recvq会blockgoutine，记录了两个block的goutine，他是个链表

发送时发生了什么
当routine是nil时会抛出unreachable的错误，已关闭会发出send on closed channel的错误。这个gouroutine被channel阻塞，数据直接发送到goroutine。接收到信息后goready，调度器会执行goroutine的代码

channel发送数据都是复制

select 多任务
采用的是轮询的数据，消息过来后根据type等判断



### Tips

#### 背景
工作中用到的php项目比较多，因此学习了下如何在linux环境debug php源码，这里介绍下过程。

#### 准备
php7.1 源码（官网下载）
linux centos-7

#### 过程

1、下载代码
`wget -o https://www.php.net/distributions/php-7.1.30.tar.gz `

2、解压
` tar -zxvf php-7.1.30.tar.gz`

3、编译

```
./configure --prefix=/home/xiaoju/php7 --enable-fpm --enable-debug
make
make install
```

--enable-debug参数表示是debug模式，后面才能用gdb断点研究

### 安装gdb
```
yum install gcc-c++
yum install gcc
yum install gdb
```

### 断点查看
```
gdb ~/php7/bin/php
r test.php
```

### Share

https://www.jianshu.com/p/2bc43d16a3a6
