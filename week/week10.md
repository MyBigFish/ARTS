### Algorihm
```
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {
        Arrays.sort(nums);
        
        List<List<Integer>> res = new ArrayList<>();
        Map<String,String> containsKey = new HashMap<>();
    
        for (int i = 0; i < nums.length - 2; i++) {
            int d0 = nums[i];
            System.out.println(i);
            int target = 0 - d0;
            int l = i + 1;
            int r = nums.length - 1;
            
            while (l < r) {

                if (nums[l] + nums[r] > target) {
                    r--;
                    continue;
                }
                
                if (nums[l] + nums[r] < target) {
                    l++;
                    continue;
                }

                List<Integer> tmp = new ArrayList<>();
                tmp.add(d0);
                tmp.add(nums[l]);
                tmp.add(nums[r]);

                String key = tmp.toString();
            
                if(containsKey.containsKey(key)) {
                    r--;
                    continue;
                }
                res.add(tmp);

                containsKey.put(key, "");

                r--;
            }


        }


        return res;
    }
}
```

### Review

本文主要讨论缓存设置策略。并不是所有的数据都要在同一时间使用，我们可以在内存中替换这些数据。
3.1 架构中的CPU缓存
最简的CPU架构是FSB将数据缓存到Cache，然后cache到CPU cache.后来添加了三级缓存，如果是多个处理机器，多核，多线程，则不同的核有独立的资源调度，除了对外连接。thread共享一级缓存，core有独立的一级缓存。
cpu不能包含所有缓存，因此他存储了指针，通过指针查找数据，每个缓存有有tag标记。当cup需要内存的内容时，他会被加载到1级缓存。他们会被分为三部分，Tag，Cache Set 和Offset.当修改数据的时候回加载数据，这时候是脏数据，写入完成后脏标记被清除。当探测是信息不可访问时，信息的copy会被标记为无效，使用的时候重新加载。
两条法则：1、脏数据不会进入任何处理器 2、干净的内存拷贝可以存在任何内存中。
完全关联内存会通过对比tag的方式查找内存。

### Tips

laravel 转发api的请求
```
return response()->streamDownload(function () {
    echo GitHub::api('repo')
                ->contents()
                ->readme('laravel', 'laravel')['contents'];
}, 'laravel-readme.md');
```

### Share

golang 什么时候用指针

1、map，slice和chan默认是指针，这个使用的时候注意

2、golang默认是值拷贝，因此使用传递指针才可以改变原来参数的值，这个时候使用指针，特别是反射，传递指针才能改变值

3、使用gin框架，把gin的上线文放到协程的时候需要copy上下文，不要直接用指针

4、大部分情况用指针可以省内存，如serive类等。

