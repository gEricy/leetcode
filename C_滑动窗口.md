

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
            # 1.出窗口，保证队列单调
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

