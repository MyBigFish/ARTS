## Algorithm

#### 题目
Given a binary tree, return the bottom-up level order traversal of its nodes' values. (ie, from left to right, level by level from leaf to root).

For example:
Given binary tree [3,9,20,null,null,15,7],
3
   / \
  9  20
    /  \
   15   7
return its bottom-up level order traversal as:
[
  [15,7],
  [9,20],
  [3]
]
#### 代码实现

```
import java.util.*;
class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        if(root == null){
            return res;
        }
        List<Integer> tmp = new ArrayList<>();
        tmp.add(root.val);
        res.add(tmp);

        walkTree(res, root, 1);

        List<List<Integer>> copy = new ArrayList<>();

        for (int i = res.size() - 1; i >= 0; i--) {
            copy.add(res.get(i));
        }

        return copy;
    }

    public void walkTree(List<List<Integer>> res,TreeNode node,int index){

        if(node == null){
            return;
        }
        
        List<Integer> tmp;
        if(index < res.size()){
            tmp = res.get(index);
        } else {
            tmp = new ArrayList<>();
        }

        if(node.left != null){
            tmp.add(node.left.val);
        }
        if(node.right != null){
            tmp.add(node.right.val);
        }

        if(tmp.size() == 0){
            return;
        }

        if(index < res.size()){
            res.set(index, tmp);
        } else {
            res.add(tmp);
        }
        index = index + 1;
        if(node.left != null){
            walkTree(res, node.left,index);
        }
        if(node.right != null){
            walkTree(res, node.right,index);
        }

        return ;
    }


}
```

## REVIEW
[Testing Streamed Responses in Laravel](https://laravel-news.com/testing-streamed-responses-in-laravel)
使用laravel一直困惑怎么测试流式下载的文件内容，这篇文章非常详细的记录了这种单元测试的实现

### 1、为什么要流式下载
     * 转发下载的内容，避免中间保存到磁盘
     

### 2、流式下载实现
```
     return response()->streamDownload(function () {
            echo $this->writer->getContent();
        }, 'users.csv');
```

### 3、测试实现

```
        $response = $this->actingAs($users->first())->get('/users/export');
        $response->assertHeader('Content-Disposition', 'attachment; filename=users.csv');

        $reader = CsvReader::createFromString($response->streamedContent());
        $reader->setHeaderOffset(0);

        $this->assertCount(User::count(), $reader);
 ```
 
 ## TIPS
 php 避免写 else 和循环的小技巧
 
 * 抽成方法
 * array_map 遍历
 * array_filter 过滤
 * 用map代替if判断

## SHARE
IT工程师的时间管理小工具推荐
在公司开发的模块多了，或者开始带人，会发现事情特别多，这个时候todo管理就特别重要了。这里推荐一个工具 https://kanbanflow.com。

应该管理什么
之前尝试过只把工作的事情列进去，发现不可以，因为还有很多杂事需要处理，例如快递、报销、安排会议。因此要把自己所有todo项都列出来。

为什么安排
人的注意力是很有限的，如果同时思考多个事情估计都做不好。尤其是敲代码和管理一起处理的时候。

怎么安排
我自己有些心得
* 划分优先级，通过不同的颜色标志。高优的先做。
* 同一时间只做一件事，不要顺手做其他事情。因为多了一件事，后面可能还有其他事情。
* 每次切换任务添加进度和下一步计划。因为随时可能有任务插进来，这个安排要有弹性，切花的时候相当于做好归档
