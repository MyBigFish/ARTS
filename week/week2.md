
## Algorithm

#### 题目
Longest Substring Without Repeating Characters
 *
 * https://leetcode.com/problems/longest-substring-without-repeating-characters/description/
 *
 * algorithms
 * Medium (28.12%)
 * Total Accepted:    875.1K
 * Total Submissions: 3.1M
 * Testcase Example:  '"abcabcbb"'
 *
 * Given a string, find the length of the longest substring without repeating
 * characters.
 * 
 * 
 * Example 1:
 * 
 * 
 * Input: "abcabcbb"
 * Output: 3 
 * Explanation: The answer is "abc", with the length of 3. 
 * 
 * 
 * 
 * Example 2:
 * 
 * 
 * Input: "bbbbb"
 * Output: 1
 * Explanation: The answer is "b", with the length of 1.
 * 
 * 
 * 
 * Example 3:
 * 
 * 
 * Input: "pwwkew"
 * Output: 3
 * Explanation: The answer is "wke", with the length of 3. 
 * ⁠            Note that the answer must be a substring, "pwke" is a
 * subsequence and not a substring.


#### 解题思路
采用动态规划的思路处理，每个元素都是某几个数组的最后一个。

#### 代码实现
```
class Solution {
    public int lengthOfLongestSubstring(String s) {

        if(s.equals("")){
            return 0;
        }

        if(s.length() == 1){
            return 1;
        }

        //动态规划的方法处理
        char[] chars = s.toCharArray();
     
        int[] dp = new int[chars.length];
        dp[0] = 1;
        int startIndex = 0;
        for (int i = 1; i < chars.length; i++) {
            char tmp = chars[i];
            int j = i - 1;
            dp[i] = 1;
        
            while(j >= startIndex){
                if(chars[j] == tmp){
                    startIndex = j + 1;
                    break;
                }
                dp[i] = dp[i] + 1;
                j--;
            }
        }

        System.out.print(Arrays.toString(dp));
     
        int maxLength = 1;
        for (int i = 0; i < dp.length; i++) {
            if(maxLength < dp[i]){
                maxLength = dp[i];
            }
        }

        return maxLength;

    }
}
```

## REVIEW

[Best Practices for Modern PHP Development](https://www.airpair.com/php/posts/best-practices-for-modern-php-development)

从事php开发一年多了，看了这篇文章还是有些理解的。
#### 1、 使用当前版本
无论是php还是php扩展，都要及时更新。这个起码是安全需要。一个无人维护的版本是有很大安全隐患的。自己可以准备一个checklist，每个月检查下。从nginc,php到使用的web框架和依赖包等等。

#### 2、初始化设置

* 时区。对于中国来说是东八区。如果是国际化的，建议使用utc时区。当然有的项目是从中国发展起来的，历史原因都要设置成北京时间，这个就看总体定位了，必然需要很多转换成本。
* session设置下存储位置，不要放在默认的/tmp文件夹下，容易有安全风险
* cookie设置成httponly。这个我这不同意，因为有时候js需要通过cookie获取一些数据。这个可以通过csrf验证等方式解决。
* 推荐一个检测工具：https://github.com/psecio/iniscan

#### 3、关闭不使用的扩展

```
php -i
```

#### 4、使用composer工具
这个没什么可说的，包管理工具可以更好的解放程序员接入其他模块的成本，解决版本冲突等问题。


#### 5、遵循设计原则
* SOLID
  * 单一职责
  * [开闭原则](https://www.cnblogs.com/gaochundong/p/open_closed_principle.html)
  * Liskov Substitution Principle(李氏替换原则)
  * 接口分离原则-Interface Segregation Principle
  * Dependency Inversion Principle 依赖翻转。

#### 6、Object calisthenics
  * 避免过多缩进，采用array_map等方法处理一些循环
  * 尽量不要使用else。可以抽取成方法处理。

#### 7、使用单元测试
  `Unit testing forces you to feel the pain of bad design up front - Michael Feathers`
  单元测试会使你感受到坏设计的痛
  
  目前为止我感觉laravel就是满足了这些设计原则和方便测试的，建议使用。

## TIPS
php [mockery](https://github.com/mockery/mockery) 常用的mock方法

```
class UserServiceTest extends TestCase
{

    public function tearDown()
    {
        Mockery::close();
    }

    /**
     * mock 静态方法
     */
    public function testStaticMethod(){
        $mock = \Mockery::mock('alias:UserService');
        $mock->shouldReceive('getSessionId')
            ->andReturn(1001);
        $this->assertEquals(1001, $mock->getSessionId());
    }

    /**
     * mock 全部成员方法
     */
    public function testMemberMethod(){
        $service = Mockery::mock('UserService');
        $service->shouldReceive('getUserInfo')
            ->andReturn(['name'=>'test']);
        $this->assertEquals(['name'=>'test'], $service->getUserInfo());
    }

    /**
     * mock 一部分方法,至mock getUserInfo
     */
    public function testPartialMemberMethod(){
        $mock = \Mockery::mock(UserService::class)->makePartial();
        $mock->shouldReceive('getUserInfo')
            ->andReturn(['name'=>'test']);
        $this->assertEquals(['name'=>'test'], $mock->getUserInfo());
        $this->assertEquals(10, $mock->getAge());
    }

    /**
     * mock 特定的参数
     */
    public function testPartialMemberMethodWithArg(){
        $mock = \Mockery::mock(UserService::class)->makePartial();
        $mock->shouldReceive('isAdult')
            ->with(16)
            ->andReturn(true);
        $this->assertEquals(true, $mock->isAdult(16));
        $this->assertEquals(false, $mock->isAdult(15));
        $this->assertEquals(true, $mock->isAdult(18));
    }
    

}
```

## SHARE
### laravel 安全check
#### 1、软件版本检测
如果使用的版本已经停止维护了，黑客就可能对其发动攻击，因此及时更新版本是很必要的，尤其是对外的接口。
* [php](https://www.php.net/supported-versions.php)，7.0将在7个月内停止安全的维护。5.x的版本已经不在维护。
* [nginx](https://www.nginx.com/faq/what-versions-of-nginx-plus-are-supported/),nginx仅支持两年以内的版本,[Legacy versions是不支持的](https://www.ruby-forum.com/t/how-does-nginx-set-end-of-life-for-older-versions/241767/2)

* laravel，[以5.5版本为例](https://laravel.com/docs/5.5/releases#support-policy)，[发版日历](https://laravel-news.com/laravel-release-process)，5.5维护到2020年8月30日

其他功能可以自行检查

#### 2、xss漏洞防御
针对这种攻击加个middleware过滤下参数就可以了
```
class XSSProtection
{
    /**
     * The following method loops through all request input and strips out all tags from
     * the request. This to ensure that users are unable to set ANY HTML within the form
     * submissions, but also cleans up input.
     *
     * @param Request $request
     * @param callable $next
     * @return mixed
     */
    public function handle(Request $request, \Closure $next)
    {
        if (!in_array(strtolower($request->method()), ['put', 'post'])) {
            return $next($request);
        }
        
        $input = $request->all();

        array_walk_recursive($input, function(&$input) {
            $input = strip_tags($input);
        });

        $request->merge($input);

        return $next($request);
    }
}
```

#### 3、csrf漏洞防御
这个参见[这篇文章](https://www.jianshu.com/p/38e355e90560)

#### 4、接口防刷
这个有多种防御方法
* 登录和修改密码接口添加验证码
* 针对接口添加次数限制，laravel自带或者统一在入口层添加

#### 5、数据隔离
* 敏感信息如用户id等从session读取，不要直接从接口返回。
* 敏感图片添加时间和登录限制。例如把图片的key放到redis，过期自动清除，依赖key反解出图片的原始地址。


#### 6、防止sql注入
* 参数在controller层添加校验
* sql采用预处理的方式 例如：`update `users` set `password` = ?, `updated_at` = ? where `id` = ?`
