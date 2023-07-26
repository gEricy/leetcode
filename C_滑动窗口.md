

滑动窗口

3. 无重复字符的最长子串

30. 串联所有单词的子串

76. 最小覆盖子串

159. 至多包含两个不同字符的最长子串

209. 长度最小的子数组

239. 滑动窗口最大值

567. 字符串的排列

632. 最小区间

727. 最小窗口子序列

424. 替换后的最长重复字符

480. 滑动窗口中位数 



### [3] 无重复字符的最长子串

- 该题，实际上是”hash表的应用“，最核心的点是`l的更新`

给定一个字符串，请你找出其中不含有重复字符的最长子串的长度。

```python
class Solution(object):
    def lengthOfLongestSubstring(self, s):
        hash = {} # {元素, 元素所处下标} = {s[i], i}
        l = 0
        ans = 0
        for r in range(len(s)):
            if s[r] in hash:
                l = max(l, hash[s[r]]+1)  # 关键是取max
            hash[s[r]] = r
            ans = max(ans, r-l+1)
        return ans
```


### [239]滑动窗口最大值 (hard)

滑动窗口与单调队列的结合 

- 注意点
  - 单调队列中，保存的是`数组下标index`，而不是数组值nums[index]
  - 什么时候，ans中添加结果？ 窗口大小 >= k时，即：当 r-1 >= k
- 步骤
  1. 索引r从0开始，一直向后滑动
  2. 新元素nums[r]进队列之前，必须保证队列的单调性(单调递减)
     - 踢除 nums[r] > nums[queue[-1]] 的 nums[queue[-1]]
  3. 元素入队：queue.append(r)
  4. 元素进队列前，可能导致窗口中元素的个数超过窗口大小，需要删除队头元素
     - r-`queue[0]`+1 > k，要剔除队头
  5. 当形成窗口时，保存窗口的最大值到ans：当`r-l > k`时，ans.append(nums[queue[0]])


说明：queue直接使用[]，会超时，使用collections.deque()就不会了

```python
class Solution(object):
    def maxSlidingWindow(self, nums, k):
        ans = []
        queue = []  # 窗口，保存了下标

        for r in range(len(nums)):
            # 1.出窗口，保证队列单调(递减)
            while queue and nums[r] > nums[queue[-1]]:
                queue.pop(-1)
            # 2.r入窗口
            queue.append(r)
            # 3.确保窗口中的元素个数，不超过窗口大小
            if r-queue[0]+1 > k:
                queue.pop(0)
            # 4.当r+1 >= k时，必然形成窗口，保存结果
            if r+1 >= k:
                ans.append(nums[queue[0]])

        return ans
```

### 3.1.2. 😄面试题59 - II. 队列的最大值

请定义一个队列并实现函数 max_value 得到队列里的最大值，要求函数max_value、push_back 和 pop_front 的均摊时间复杂度都是O(1)。

若队列为空，pop_front 和 max_value 需要返回 -1

```python
class MaxQueue:

    def __init__(self):
        queue = []     # 原队列
        max_queue = [] # 单调递减队列

    def max_value(self) -> int:
        return self.max_queue[0] if len(max_queue) > 0 else -1

    def push_back(self, value: int) -> None:
        # 保证单调递减队列的性质
		while len(max_queue) > 0 and value > max_queue[-1]:
            max_queue.pop(-1)
        max_queue.append(value)
        # 原始队列
		queue.append(value)

    def pop_front(self) -> int:
        if not self.queue:
            return -1
        popVal = queue.pop(0) # 待弹出元素
        if popVal == max_queue[0]: # max_queue的首元素 == 待弹出元素，将其弹出
            max_queue.pop(0)
        return res
```

---

#### 简介

双指针算法有两个重要的使用，一种是两个序列，一个序列一个指针，比如归并排序的归并过程，另一种就是针对一个序列，使用两个指针，滑动窗口也是属于这一种。



#### 模板

使用传统的方法来解决双指针问题，时间复杂度会比较高。

```java
for(int i=0;i<;i++){
    for(int j=0;j<;j++){
    }
}
```

使用双指针算法重点是找到问题的单调性，将O(n^2)优化成O(n)，常见的模板如下

```java
for(int i=0,j;i<;i++){
    while(j<len&&check(j))j++;  // 判断j的范围，以及判断后面指针什么条件下进行移动
}
```



#### 例题

#### [无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)



##### 思路

找到每个字符开头的最长无重复的字串(使用hash表来进行快速判断是否序列出现重复)，将结果长度进行记录，找出最小值。

介绍一下滑动窗口的尾指针是怎样维护的，当hash表中没有出现该元素就进行添加。



 ##### 模板使用

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        // hash表
        HashSet<Character>hash=new HashSet<>();
        int res=0;
        // i是开头 j是结尾
        for(int i=0,j=0;i<s.length();i++){
            while(j<s.length()&&!hash.contains(s.charAt(j))){
                hash.add(s.charAt(j));
                j++;
            }
            hash.remove(s.charAt(i));
            res=Math.max(res,j-i);
        }
        return res;
    }
}
```



#### [76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)



##### 思路

这道题是一道困难题，主要难在怎样判断一个字符串是否覆盖一个字符串。假如说可以实现这样一个功能，咱么尝试写一下



```c++
public String minWindow(String s, String t) {
    String res="";
    for(int i=0,j=0;i<s.length();i++){
        while(j<s.length()&&!check(i,j))j++;
        // 此时如果j<s.length()证明（i，j）是符合条件的序列
        if(i<s.length()){
            if(res==""||res.length()>j-i+1)res=s.substring(i,j+1);
        }
    }
    return res;
}


```



完整效率低的代码

```java
class Solution {

    String t,s;
public String minWindow(String _s, String _t) {
     s=_s;
     t=_t;
    String res="";
    for(int i=0,j=0;i<s.length();i++){
        while(j<s.length()&&!check(i,j))j++;
        // 此时如果j<s.length()证明（i，j）是符合条件的序列
        if(j<s.length()){
            if(res==""||res.length()>j-i+1)res=s.substring(i,j+1);
        }
    }
    return res;
}

boolean check(int i,int j){
    if(i>j)return false;
    int []map=new int[128];
    for(int x=i;x<=j;x++)map[s.charAt(x)]++;
    for(int x=0;x<t.length();x++){
        if(map[t.charAt(x)]--<=0)return false;
    }
    return true;
}
}
```

