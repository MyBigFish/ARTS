### Algorithm

回文字符串 Longest Palindromic Substring
```
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

/*
 * @lc app=leetcode id=5 lang=java
 *
 * [5] Longest Palindromic Substring
 */
class Solution {
    public String longestPalindrome(String s) {
        int length = s.length();
        if(length == 1){
            return s;
        }
 
        List<Character> sNew = new ArrayList<>();
        //add #
        for (int i = 0; i < length; i++) {
            sNew.add('#');
            sNew.add(s.charAt(i));
        }
        sNew.add('#');

        int id = 0;
        int mxRight = 0;
        Integer[] Len = new Integer[sNew.size()];
  
        for (int i = 0; i < sNew.size(); i++) {
            Len[i] = 0;
        }
        Len[0] = 1;

        for (int i = 1; i < sNew.size(); i++) {
            Integer minLength = mxRight > i ? Math.min(Len[2 * id - i], mxRight - i):1;
            Len[i] = minLength;

            while (true) {
                if(i + Len[i] >= sNew.size()){
                    break;
                }

                if (i - Len[i] < 0){
                    break;
                }
                char cl = sNew.get(i + Len[i]);
                char cr = sNew.get(i - Len[i]);

            
                if(cl != cr){
                    break;
                }
                Len[i]++;
                if(i + Len[i] > mxRight){
                    mxRight = i + Len[i];
                    id = i;
                }
            }

    
        }
        //get minx
        int maxLength = 0;
        int maxIndex = 0;
        for (int i = 0; i < Len.length; i++) {
            if(maxLength < Len[i]){
                maxLength = Len[i];
                maxIndex = i;
            }
        }
        
        String sNewStr = "";
        for (int i = 0; i < sNew.size(); i++) {
            sNewStr = sNewStr + sNew.get(i);
        }
   
        String sub = sNewStr.substring(maxIndex - maxLength + 1,maxIndex + maxLength - 1);

        return sub.replace("#", "");
        
    }
}


```

### Review

原文：https://povilasv.me/go-scheduler/
笔记
基础
go的运行时管理调度，垃圾回收和运行环境，这里只关注调度。
运行时调度协成，将他们映射到线程上执行。协程是轻量级的线程，启动成本很低。
协程的结构体：https://github.com/golang/go/blob/5dd978a283ca445f8b5f255773b3904497365b61/src/runtime/runtime2.go#L332
运行时监控每一个G的状态，映射到逻辑处理器上（P）。P可以认为是个抽象的资源或者上下文，线程运行P。
我们可以通过设置runtime.GOMAXPROCS来控制逻辑处理器的个数。
操作系统最终是通过线程来运行代码。go通过编译器把运行时的调用插入到多个地方，例如channel，这样go就可以通知调度器和执行代码。

MPG的关系
从图中可以看出，有两种存储G的队列，全局和本地。每个P都维护一个G的队列。M通过绑定一个P来执行协程。M会弹出一个P的G来执行代码。
当执行go func时一个G会进入P的队列。这里有个偷取的算法，当M执行完G的时候回随机从其他P获取G，获取P队列中的一半G。
当有阻塞性的调用时，这个调用会被终止，同时运行时会切换到新的线程执行这个P的其他G。
当系统调用恢复时，G会被重新放到本地队列，这个线程插入到空闲线程列表中。以下会阻塞:

 - 系统调用（打开文件）
 - 网络输入
 - channel操作
 - 同步包里的原生操作

调度器追踪
go允许追踪运行时的调度器。添加GODEBUG调度器就可以。


### Tips
获取本地ip
```
func getInnerIP() string {
	gInnerIP := "127.0.0.1"
	netInterfaces, err := net.Interfaces()
	if err != nil {
		fmt.Println("net.Interfaces failed, err:", err.Error())
		return ""
	}

	for i := 0; i < len(netInterfaces); i++ {
		if (netInterfaces[i].Flags & net.FlagUp) != 0 {//排除无用网卡
			addrs, _ := netInterfaces[i].Addrs()

			for _, address := range addrs {
				if ipnet, ok := address.(*net.IPNet); ok && !ipnet.IP.IsLoopback() {
					if ipnet.IP.To4() != nil {
						fmt.Println(ipnet.IP.String())
						return ""
					}
				}
			}
		}
	}

	return gInnerIP
}
```



### Share
php larvel和golang gin使用心得
同时使用这俩框架很久了，不免有些对比，这里记录下。

1、使用场景
laravel主要用于对内的业务，并发不高，偏后台的功能。gin主要应用于对外的服务，接口不多，但是性能要求高

2、单元测试
laravel的单测使用比gin的方便，而且laravel的有phpStorm的完美支持。gin的如果传参等需要单独配置工具，环境切换模块也需要自己开发。

3、开发效率
使用久了发现laravel的效率明显高于gin，毕竟是php的。gin的效率也不能说差，如果应用与后台的增删改查，还是php的开发速度快。

4、安全性
laravel用php开发，弱类型，类型强校验要求高的还是gin

5、问题debug
gin依赖rd学西一些工具才能debug线上问题，如内存泄露等，laravel很少出现这个问题。

总的来说，对外要求高的用gin，对内的用laravel
