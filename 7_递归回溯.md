

-[93] 复原 IP 地址 


---

递归回溯 、 dfs深度优先遍历


1. 先画出二叉树的结果图
2. 采用dfs执行深度优先遍历 （dfs: 不撞南墙不回头）


```python
def xxx(nums):
    ans = []
    land = []

    def dfs():
        if 满足递归退出条件
            ans.append(land)
            return
        for 元素 in range(可选集合):
            land.append(元素)  # 选中一个元素
            dfs(下一个可选集合)
            land.pop()        # 弹出刚才选中的元素

    dfs()
    return ans
```

# 1. 子集\组合

### 1.1. [78] 子集

无重复元素

```python
输入: nums = [1,2,3]
输出:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

```python
class Solution(object):
    def subsets(self, nums):
        size = len(nums)
        ans = []

        def dfs(land, start):
            ans.append(land[:])
            for i in range(start, size):
                land.append(nums[i])
                dfs(land, i+1)
                land.pop()

        dfs([], 0)

        return ans
```

### 1.2. [90] 子集 II

给定一个可能包含`重复元素`的整数数组nums，返回该数组所有可能的子集

说明：解集不能包含重复的子集

```python
输入: [1,2,2]
输出:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```

```python
class Solution(object):
    def subsetsWithDup(self, nums):
        nums.sort() # 剪枝前，必须排序
        size = len(nums)
        ans = []

        def dfs(land, start):
            ans.append(land[:])
            for i in range(start, size):
                if i>start and nums[i] == nums[i-1]: # 剪枝
                    continue
                land.append(nums[i])
                dfs(land, i+1)
                land.pop()

        dfs([], 0)
        return ans
```

### 1.3. [77] 组合

给定两个整数 n 和 k，返回范围 [1, n] 中所有可能的 k 个数的组合。

你可以按 任何顺序 返回答案。

```python
输入: n = 4, k = 2
输出:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

```python
class Solution(object):
    def combine(self, n, k):
        nums = [i+1 for i in range(n)]
        size = len(nums)
        ans = []

        def dfs(land, start):
            if len(land) == k:
                ans.append(land[:])
                return
            for i in range(start, size):
                land.append(nums[i])
                dfs(land, i+1)
                land.pop()

        dfs([], 0)
        return ans
```

### 1.4. [39] 组合总和

给你一个 无重复元素 的整数数组 candidates 和一个目标整数 target ，找出 candidates 中可以使数字和为目标数 target 的 所有不同组合 ，并以列表形式返回。你可以按 任意顺序 返回这些组合。

candidates 中的 同一个数字可以`无限制重复`被选取。如果至少一个数字的被选数量不同，则两种组合是不同的。 


```python
class Solution(object):
    def combinationSum(self, candidates, target):
        size = len(candidates)
        ans = []

        def dfs(land, start, target):
            if target < 0:
                return
            if target == 0:
                ans.append(land[:])
                return
            for i in range(start, size):
                land.append(candidates[i])
                dfs(land, i, target-candidates[i])
                land.pop()

        dfs([], 0, target)
        return ans
```

### 1.5. [40] 组合总和 II

给你一个由候选元素组成的集合 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的每个元素在每个组合中只能使用一次 。

注意：解集不能包含重复的组合。

```python
class Solution(object):
    def combinationSum2(self, candidates, target):
        candidates.sort()
        size = len(candidates)
        ans = []

        def dfs(land, start, target):
            if target < 0:
                return
            if target == 0:
                ans.append(land[:])
                return
            for i in range(start, size):
                if i>start and candidates[i] == candidates[i-1]:
                    continue
                land.append(candidates[i])
                dfs(land, i+1, target-candidates[i])
                land.pop()

        dfs([], 0, target)
        return ans
```

### 1.6. [216] 组合总和 III

找出所有相加之和为 n 的 k 个数的组合，且满足下列条件：

- 只使用数字1到9
- 每个数字 最多使用一次 

返回 所有可能的有效组合的列表 。该列表不能包含相同的组合两次，组合可以以任何顺序返回。

 

```python
class Solution(object):
    def combinationSum3(self, k, n):
        nums = [i+1 for i in range(9)]
        size = len(nums)
        ans = []

        def dfs(land, start, target):
            if len(land) > k or target < 0:
                return
            if len(land) == k and target == 0:
                ans.append(land[:])
                return
            for i in range(start, size):
                land.append(nums[i])
                dfs(land, i+1, target-nums[i])
                land.pop()

        dfs([], 0, n)
        return ans
```


### 1.6. [377] 组合总和 Ⅳ

给你一个由 不同 整数组成的数组 nums ，和一个目标整数 target 。请你从 nums 中找出并返回总和为 target 的元素组合的个数。

题目数据保证答案符合 32 位整数范围。


说明：该题是dp，不是递归回溯

---


# 2. 全排列

### 2.1. [46] 全排列

给定一个 没有重复 数字的序列，返回其所有可能的全排列。

```python
class Solution(object):
    def permute(self, nums):
        size = len(nums)
        use = [0 for _ in range(size)]
        ans = []

        def dfs(land):
            if len(land) == size:
                ans.append(land[:])
                return
            for i in range(size):
                if use[i] == 1:
                    continue
                land.append(nums[i])
                use[i] = 1
                dfs(land)
                land.pop()
                use[i] = 0

        dfs([])
        return ans
```

### 2.2. [47] 全排列 II

给定一个可包含重复数字的序列 nums ，按任意顺序 返回所有不重复的全排列。


```python
class Solution(object):
    def permuteUnique(self, nums):
        nums.sort()
        size = len(nums)
        use = [0 for _ in range(size)]
        ans = []

        def dfs(land):
            if len(land) == size:
                ans.append(land[:])
                return
            for i in range(size):
                if i>0 and nums[i] == nums[i-1] and use[i-1] == 1:
                    continue
                if use[i] == 1:
                    continue
                land.append(nums[i])
                use[i] = 1
                dfs(land)
                land.pop()
                use[i] = 0

        dfs([])
        return ans
```



# 3.应用题

### 3.1. [22] 括号生成

数字 *n* 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 **有效的** 括号组合。

```shell
输入：n = 3
输出：[
       "((()))",
       "(()())",
       "(())()",
       "()(())",
       "()()()"
     ]
```

解：生成n对括弧，那么结果就是2n，设置left/right两个变量，分别记录左/右括弧出现的次数

每次，选中左/右括弧，相应的left/right都+1。当满足left+right=2n时，得到一个括弧

「注」在选择左/右括弧时，是有条件的！

「注」每次选中，都要对left/right +1，然后，将括弧加入land中，再次backtrace

1. 选中左括弧条件: left < n，即左括弧的个数，没到n个
2. 选中右括弧条件: left > right，左括弧个数比右括弧个数多

```python
class Solution(object):
    def generateParenthesis(self, n):
        ans = []
        def dfs(left, right, land):
            if left == n and right == n:
                ans.append(land)
                return
            if left < n:
                dfs(left+1, right, land+"(")
            if left > right:
                dfs(left, right+1, land+")")

        dfs(0, 0, "")
        return ans
```


### 3.2. [17] 电话号码的字母组合

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

```c
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

```python
class Solution(object):
    def letterCombinations(self, digits):
        size = len(digits)
        if size == 0:
            return []
        ans = []

        map = {
            "2": "abc",
            "3": "def",
            "4": "ghi",
            "5": "jkl",
            "6": "mno",
            "7": "pqrs",
            "8": "tuv",
            "9": "wxyz",
        }

        def dfs(start, land):
            if len(land) == size: # 递归退出条件: land的个数 == 按键个数
                ans.append(land)
                return
            for i in range(start, size):  # 遍历每个按键
                for ch in map[digits[i]]:  # 遍历每个按键对应的字符
                    dfs(i+1, land+ch)

        dfs(0, "")
        return ans
```




### 3.3. [93] 复原 IP 地址

```
输入：s = "25525511135"
输出：["255.255.11.135","255.255.111.35"]

输入：s = "0000"
输出：["0.0.0.0"]

输入：s = "1111"
输出：["1.1.1.1"]
```



