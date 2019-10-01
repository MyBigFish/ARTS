### Algorihm

Given an array nums of n integers and an integer target, find three integers in nums such that the sum is closest to target. Return the sum of the three integers. You may assume that each input would have exactly one solution.

Example:

Given array nums = [-1, 2, 1, -4], and target = 1.

The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).

```

import java.util.Arrays;

/*
 * @lc app=leetcode id=16 lang=java
 *
 * [16] 3Sum Closest
 */
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        //sort
        Arrays.sort(nums);
        int res = 0;
        boolean isInit = true;
        for (int i = 0; i < nums.length; i++) {
            
            int tmp = target - nums[i];
            int left = i + 1;
            int right = nums.length - 1;
            while(left < right){
                int tmpSum = nums[i] + nums[left] + nums[right];
                if (isInit == true) {
                    res = tmpSum;
                    isInit = false;
                    continue;
                }
                if (nums[left] + nums[right] == tmp){
                    return target;
                }
                if(Math.abs(target - tmpSum) < Math.abs(target - res)) {
                    res = tmpSum;
                }
                if (nums[left] + nums[right] > tmp){
                    right--;
                    continue;
                }
                left++;

            }

        }
        return res;
    }
}

```

### Review
[Garbage Collection In Go](https://www.ardanlabs.com/blog/2018/12/garbage-collection-in-go-part1-semantics.html)
标记启动STW，打开写屏障，通知保存数据 ,保持数据状态。通常10到30纳秒。每个运行的goroutine都要停止。第一件事是占用25%的cpu容量，每4个协程就有一个在在执行回收工作。
并发标记，标记堆中正在使用的值。他会所有的协程，查找指向堆的指针。收集器会遍历这些跟指针。MarkAssist,帮助更快的收集。帮助是时间是加入到堆中的内存决定的。
标记结束。关闭写的屏障，所有协程恢复工作
启动时机：GC Percentage :默认100，决定了下次回收后可以申请的内存。当快超过100%的应用加入是启动垃圾回收。


### Tips
https://www.jianshu.com/p/f6af567b25a7

### Share
https://www.jianshu.com/p/fdd9d4a3771a
