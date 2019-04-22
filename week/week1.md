
## Algorithm

#### 题目
Given an integer array nums, find the contiguous subarray (containing at
least one number) which has the largest sum and return its sum.
 
Example:

Input: [-2,1,-3,4,-1,2,1,-5,4],
Output: 6
Explanation: [4,-1,2,1] has the largest sum = 6.
Follow up:
 
If you have figured out the O(n) solution, try coding another solution using
the divide and conquer approach, which is more subtle.

#### 解题思路
采用动态规划的方法。每个元素都是某个数组最后一个元素。他与下一个元素i相加获取结果A，比较i和A的大小，大的加入数组dp[i]。

#### 代码实现
```
class Solution {
    public int maxSubArray(int[] nums) {

        if(nums == null){
            return 0;
        }

        if(nums.length == 1){
            return nums[0];
        }

        int length = nums.length;
        int[] dp = new int[length];
        int maxNum = nums[0];
        dp[0] = maxNum;
        for (int i = 1; i < length; i++) {
            int tmp = dp[i - 1] + nums[i];
            if(tmp < nums[i]){
                tmp = nums[i];
            }
            dp[i] = tmp;
            maxNum = tmp > maxNum ? tmp:maxNum;
        }
        return maxNum;
    }
}
```

## REVIEW

[5 Advanced Go Testing Techniques](https://hackernoon.com/5-advanced-go-testing-techniques-7199b686b6c1)]

我自己对单元测试比较感兴趣，所以会看些单元测试的文章。虽然开发用的是php，但是平时还是会看些go的知识。
#### 1、 使用 test suites
使用接口定义，这样写出来的单元测试可以通用。比如接口的实现变化了。这个我觉的还是要具体分析。一些通用的工具方法推荐使用，实际的业务代码可能不需要，因为业务变化有时候是很大的，不止改接口

#### 2、避免接口污染

接口的创建为的是避免使用者和你的包强依赖。
能不加就不加。一些通用的工具方法推荐使用，实际的业务代码可能不需要，因为业务变化有时候是很大的，不止改接口

#### 3、不要开放并发能力

这样会增加复杂性，使用者需要知道数据什么时候结束，错误处理等

#### 4、使用 net/http/httptest

httptest可以帮助我们测试http请求，而不用实际启动一个服务。目前有的开源框架已经封装的很好了。


#### 5、使用隔离的_test package
防止循环引用。目前公司的golang框架存在这个问题。

## TIPS
php 除了自带的数组操作方法，laravel框架也封装了一些
array_column()
从二维数组取对应键的值，组成新数组

array_combine()
两个数组组成新的二维数组，其中的一个数组是键名，另一个数组的值为键值

array_only()
取数组对应键的值，组成新的数组

array_add()
array_add如果给定的键在数组中不存在，则该函数将给定的键/值对添加到数组中

array_collapse()
该array_collapse函数将数组数组折叠为单个数组

array_dot()
该array_dot函数将多维数组展平为单级数组，使用“点”符号表示深度

array_except()
该array_except函数从数组中删除给定的键/值对

array_forget()
该array_forget函数使用“点”表示法从深度嵌套的数组中删除给定的键/值对

array_get()
该array_get函数使用“点”表示法从深层嵌套数组中检索值

array_has()
该array_has函数使用“点”表示法检查数组中是否存在给定项目或项目

array_prepend()
该array_prepend函数将项目推送到数组的开头

## SHARE
部门新成立不久，各种流程很乱，前一段时间整理了下工作流程，这里分享下。
1、需求流程
需求的话要有粗评时间点、需求冻结时间点。这点很关键，时间点控制好了，PM才能知道什么时候提前准备好需求，哪些放在下一个版本做。当然这个不是死的，还需要针对紧急需求制定紧急流程，分为三种：删除需求，修改需求和插入需求。建议这些都要rd leader，qaleader批准。

2、开发流程
公司有专门的代码集成和cr工具，统一的预发和生产环境。因此，每个版本都会创建有相应的预发分支和生产分支。预发代码大家统一合并到预发布环境，预发测试通过，在发版日合并到生产环境。然后预发分支删除，重新checkout。

3、测试流程
case评审->线下测试->预发测试->生产测试
生产环境要有全量的checklist回归。

这个针对的web业务的开发。app端的开发天然带有版本属性，web因为可以随时上线，有时候并没有版本概念，给qa带来了极大的测试压力和代码管理的混乱。
因此要有版本概念，可以两周作为一个版本，设置好需求冻结时间、最晚提测时间、上线时间。rd owner有责任推动大家遵守版本规范。