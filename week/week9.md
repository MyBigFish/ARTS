### Algorihm

```
import com.sun.org.apache.xpath.internal.operations.Mod;

/*
 * @lc app=leetcode id=12 lang=java
 *
 * [12] Integer to Roman
 */
class Solution {
    public String intToRoman(int num) {

        if(num == 0){
            return "";
        }

        //建立映射表
        String[] arr1 = {"I","II","III","IV","V","VI","VII","VIII","IX"};
        String[] arr10 = {"X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};
        String[] arr100 = {"C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};
        String[][] arr = {arr1,arr10,arr100};

        StringBuilder sb = new StringBuilder();
        int index = 0;
        while(num > 0){
            int tmp = num % 10;
            num = num / 10;
            if(tmp == 0){
                index++;
                continue;
            }
            
            if(index == 3){
                for (int i = 0; i < tmp; i++) {      
                    StringBuilder r = new StringBuilder("M");
                    sb = r.append(sb);        
                }
                break;
            } 

            String[] tmpArr = arr[index];
            StringBuilder r = new StringBuilder(tmpArr[tmp - 1]);
            sb = r.append(sb);
            index++;
        }

        return sb.toString();

    }
}


```

### Review

[What every programmer should know about memory, Part 1](https://lwn.net/Articles/250967/)
1、第一部分对这个系列做了简短的介绍和要感谢的人。这个系列的文章主要介绍内存问题。

2、芯片组包含两部分：北桥和南桥
所有的cpu通过消息总线连接，FSB.北桥是内存控制器，不同的架构有不同。
南桥处理输入输出，PCI-E通常都连接到南桥。
cup通信通过同一个北桥的bus
RAM的通信都是通过北桥
RAM只有一个端口
CPU与南桥通信要通过北桥

这个设计会引起两个性能瓶颈问题。1、带宽争夺，RAM与DMA 2、北桥到RAM，RAM有并行的类型。为了解决这些问题，有的设计把内存控制器放到了CPU中，但是CPU之前的通信也会有损耗。
RAM分为静态和动态设计

### Tips

```
laravel持续下载,当不确定文件大小时
$this->writer = CsvWriter::createFromString('');
        $this->writer->insertOne([
            'Name', 'Email', 'Email Verified', 'Created', 'Updated'
        ]);

        User::all()->each(function ($user) {
            $this->addUserToCsv($user);
        });

        return response()->streamDownload(function () {
            echo $this->writer->getContent();
        }, 'users.csv');
```

### Share

https://www.jianshu.com/p/2c7ee512ca4c
