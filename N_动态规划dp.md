难点

- 回文: 子串、子序列
- 博弈型
- 股票问题
- 背包

# 1. 坐标型

## 1.1. dp[i] = dp[i - 1] + dp[i - 2]

### 1.1.1. [509] 斐波那契数

```python
# 方法1
class Solution(object):
    def fib(self, n):
        if n == 0:
            return 0
        if n == 1:
            return 1

        # 初始化
        dp = [0] * (n + 1)  # 0, 1, ...., n
        dp[0] = 0
        dp[1] = 1

        # dp
        for i in range(2, n + 1):
            dp[i] = dp[i - 1] + dp[i - 2]

        return dp[-1]
```

```python
# 方法2: 仅仅使用两个变量
class Solution(object):
    def fib(self, n):
        if n == 0:
            return 0
        if n == 1:
            return 1

        # 初始化
        dp = [0] * 2
        dp[0 % 2] = 0
        dp[1 % 2] = 1

        for i in range(2, n + 1):
            dp[i % 2] = dp[(i - 1) % 2] + dp[(i - 2) % 2]

        return dp[n % 2]

```


### 1.1.2. [70] 爬楼梯


```python
# 方法1
class Solution(object):
    def climbStairs(self, n):
        if n == 0:
            return 0
        if n == 1:
            return 1
        if n == 2:
            return 2

        dp = [0] * (n + 1)
        dp[0] = 0
        dp[1] = 1
        dp[2] = 2

        for i in range(3, n + 1):
            dp[i] = dp[i - 1] + dp[i - 2]

        return dp[n]
```

```python
# 方法2: 仅仅使用两个变量
class Solution(object):
    def climbStairs(self, n):
        if n == 1:
            return 1
        if n == 2:
            return 2
        
        # 初始化
        dp = [0] * 2
        dp[1 % 2] = 1
        dp[2 % 2] = 2

        for i in range(3, n + 1):
            dp[i % 2] = dp[(i - 1) % 2] + dp[(i - 2) % 2]

        return dp[n % 2]
```

---

## 1.2. 零钱兑换

### 1.2.1. [322] 零钱兑换

给你一个整数数组 coins ，表示不同面额的硬币；以及一个整数 amount ，表示总金额。

计算并返回可以凑成总金额所需的 `最少的硬币个数` 。如果没有任何一种硬币组合能组成总金额，返回 -1 。

你可以认为每种硬币的数量是无限的。

```python
class Solution(object):
    def coinChange(self, coins, amount):
        # dp[i]: 凑成总金额i所需的最少硬币个数（-1表示不能构成）
        # dp[i] = min { dp[i-coins[j]] + 1 }, i-coins[j] >= 0
        dp = [-1 for _ in range(amount+1)]

        dp[0] = 0  # 初始化: 组成0元, 使用0个硬币

        for i in range(1, amount+1):
            for coin in coins:
                if i-coin >= 0 and dp[i-coin] != -1:
                    if dp[i] == -1:  # 选择最小值
                        dp[i] = dp[i-coin]+1
                    else:
                        dp[i] = min(dp[i-coin]+1, dp[i])

        return dp[-1]
```

### 1.2.2. 😭 [518] 零钱兑换 II

给你一个整数数组 coins 表示不同面额的硬币，另给一个整数 amount 表示总金额。

请你计算并返回可以`凑成总金额的硬币组合数`。如果任何硬币组合都无法凑出总金额，返回 0 。

假设每一种面额的硬币有无限个。 

解题思路：

- 定义子问题: problem(i) = sum( problem(i-coin[j]) ), coin[j] = 1,2,5 
- 含义为凑成总金额i的硬币组合数 = 凑成总金额硬币 i-1, i-2, i-5,...的子问题之和


```python
class Solution(object):
    def change(self, amount, coins):
        dp = [0 for _ in range(amount+1)]  # 凑成总金额amount的硬币组合数
        dp[0] = 1  # 组成金额为0，需要1种方式，就是不需要任何硬币
        for coin in coins:
            for i in range(amount+1):
                if i-coin >= 0:
                    dp[i] += dp[i-coin]
        return dp[-1]
```

---

## 1.3. 路径

- dp = [[0 for _ in range(n)] for _ in range(m)]

### 1.3.1. [62] 不同路径

`dp[i][j]` : 从(0,0)到(m,n)的不同路径总个数

```python
class Solution(object):
    def uniquePaths(self, m, n):

        dp = [[0 for _ in range(n)] for _ in range(m)]

        for i in range(m):
            dp[i][0] = 1
        for i in range(n):
            dp[0][i] = 1

        for i in range(1, m):
            for j in range(1, n):
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1]

        return dp[-1][-1]
```

### 1.3.2. [63] 不同路径 II

有障碍物

```python
class Solution(object):
    def uniquePathsWithObstacles(self, obstacleGrid):
        m = len(obstacleGrid)
        n = len(obstacleGrid[0])

        dp = [[0 for _ in range(n)] for _ in range(m)]

        for i in range(m):
            if obstacleGrid[i][0] == 1:
                break
            else:
                dp[i][0] = 1

        for i in range(n):
            if obstacleGrid[0][i] == 1:
                break
            else:
                dp[0][i] = 1

        for i in range(1, m):
            for j in range(1, n):
                if obstacleGrid[i][j] == 0:
                    dp[i][j] = dp[i-1][j] + dp[i][j-1]

        return dp[-1][-1]
```

### 1.3.3. [64] 最小路径和

给定一个包含非负整数的 `m x n` 网格 `grid` ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小



```python
class Solution(object):
    def minPathSum(self, grid):
        m = len(grid)
        n = len(grid[0])

        dp = [[0 for _ in range(n)] for _ in range(m)]

        dp[0][0] = grid[0][0]

        for i in range(1, m):
            dp[i][0] = dp[i - 1][0] + grid[i][0]

        for i in range(1, n):
            dp[0][i] = dp[0][i - 1] + grid[0][i]

        for i in range(1, m):
            for j in range(1, n):
                dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j]

        return dp[-1][-1]
```

---

# 2. 字符串: 子数组、子序列、回文串


## 2.1. dp[i]的定义都是以nums[i]作为结尾的xxxx

解题思路:

1. dp[i]: 以nums[i]作为结尾的xxx（其中，nums[i]一定被选中）
2. 从dp数组中选择一个作为结果: max{ dp }



### 2.1.1. [300] 最长递增子序列


子序列可以不连续

```python
# dp[i]: 以 nums[i] 作为结尾的最长递增子序列的长度
# dp[i] = max( dp[j]+1 ), if nums[i] > nums[j], j ∈ [0,i)

class Solution(object):
    def lengthOfLIS(self, nums):
        n = len(nums)

        # 初始化: 初始为1
        dp = [1] * n

        # 遍历: 求出以nums[i]为结尾的最长递增子序列的长度
        for i in range(n):
            for j in range(i):
                if nums[i] > nums[j]:
                    dp[i] = max(dp[i], dp[j] + 1)

        # 取max
        return max(dp)
```

### 2.1.2. [53] 最大子数组和

给你一个整数数组 nums ，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

子数组 是数组中的一个连续部分。

- 子数组是连续的



```python
# dp[i]: 以nums[i]作为结尾的最大子数组和
# dp[i] = dp[i] = max(dp[i - 1] + nums[i], nums[i])

class Solution(object):
    def maxSubArray(self, nums):
        n = len(nums)

        # dp[i]: 表示以nums[i]作为结尾的最大连续子数组的和
        dp = [0 for _ in range(n)]

        dp[0] = nums[0]

        for i in range(1, n):
            # 选中nums[i]、不选中nums[i]，二者取最大的那个
            dp[i] = max(dp[i - 1] + nums[i], nums[i])

        return max(dp)
```

### 2.1.3. [152] 乘积最大子数组

给你一个整数数组 nums ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

- 子数组是连续的


```python
# 解题核心: 要保存最大值和最小值，因为乘法的负负得正，可能会得到最大乘积

# dp[2][n]
# dp[0][i] 以nums[i]作为结尾的最大乘积: max( dp[0][i-1] * nums[i], dp[1][i-1] * nums[i], nums[i] )
# dp[1][i] 以nums[i]作为结尾的最小乘积: min( dp[0][i-1] * nums[i], dp[1][i-1] * nums[i], nums[i] )

class Solution(object):
    def maxProduct(self, nums):
        n = len(nums)
        dp = [
            [0] * n,
            [0] * n,
        ]

        dp[0][0] = nums[0]
        dp[1][0] = nums[0]

        for i in range(1, n):
            a = dp[0][i - 1] * nums[i]
            b = dp[1][i - 1] * nums[i]
            dp[0][i] = max(a, b, nums[i])
            dp[1][i] = min(a, b, nums[i])

        return max(dp[0])
```



### 2.1.4. [718] 最长重复子数组

给两个整数数组 nums1 和 nums2 ，返回 两个数组中 公共的 、长度最长的`子数组`的长度 。

```python
class Solution(object):
    def findLength(self, nums1, nums2):
        # 增加一个空字符: 0表示空字符
        nums1 = [0] + nums1
        nums2 = [0] + nums2

        m = len(nums1)
        n = len(nums2)

        # dp[i][j]: 以 nums1[i] 和 nums[j] 作为结尾的，最长重复子数组的长度
        dp = [[0 for _ in range(n)] for _ in range(m)]

        # 初始化: 空字符串与任何字符串都没有重复子数组
        for i in range(m):
            dp[i][0] = 0
        for j in range(n):
            dp[0][j] = 0

        ans = 0
        for i in range(1, m):
            for j in range(1, n):
                if nums1[i] == nums2[j]: 
                    dp[i][j] = dp[i - 1][j - 1] + 1
                    ans = max(dp[i][j], ans)

        return ans
```


### 2.1.5. [1143] 最长公共子序列

```python
class Solution(object):
    def longestCommonSubsequence(self, text1, text2):
        # 填充一个空字符: 0表示空字符
        text1 = "0" + text1
        text2 = "0" + text2

        m = len(text1)
        n = len(text2)

        # dp[i][j]: nums1[0...i] 和 nums[0...j] 最长公共子序列的长度
        dp = [[0 for _ in range(n)] for _ in range(m)]

        # 初始化: 空字符串与任何字符都没有公共序列
        for i in range(m):
            dp[i][0] = 0
        for i in range(n):
            dp[0][i] = 0

        ans = 0
        for i in range(1, m):
            for j in range(1, n):
                if text1[i] == text2[j]:
                    dp[i][j] = dp[i - 1][j - 1] + 1
                else:
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
                # 更新结果
                ans = max(ans, dp[-1][-1])
                
        return ans
```

### 2.1.6. [72] 编辑距离

```python
class Solution(object):
    def minDistance(self, word1, word2):
        word1 = " " + word1
        word2 = " " + word2

        m = len(word1)
        n = len(word2)

        # dp[i][j]: word1[0,i-1]转换为word[0,j-1]的最少操作数
        dp = [[0 for _ in range(n)] for _ in range(m)]

        for i in range(m):
            dp[i][0] = i
        for i in range(n):
            dp[0][i] = i

        for i in range(1, m):
            for j in range(1, n):
                if word1[i] == word2[j]:  # 无需操作
                    dp[i][j] = dp[i-1][j-1]
                else:
                    dp[i][j] = min(dp[i-1][j-1], dp[i][j-1], dp[i-1][j]) + 1  # 插入、删除、替换

        return dp[-1][-1]

```

----


## 2.2. 🤩 回文子串、回文子序列


### 2.2.1. 最长回文子序列

#### 2.2.1.1. 😭 [516] 最长回文子序列


解法1: dp

```python
class Solution(object):
    def longestPalindromeSubseq(self, s):
        n = len(s)

        # [i...j] 最长回文子序列的长度
        dp = [[0 for _ in range(n)] for _ in range(n)]

        # 遍历[右上三角]: 从下到上，从左到右
        for i in range(n-1, -1, -1):  # for i=n-1; i>=0; i--
            for j in range(i, n):  # for j=i; i<n; i++
                if i == j:
                    dp[i][j] = 1
                else:
                    if s[i] == s[j]:
                        dp[i][j] = dp[i+1][j-1] + 2
                    else:
                        dp[i][j] = max(dp[i+1][j], dp[i][j-1])

        return dp[0][n-1]
```

解法2: 问题转化 = 1.将字符串翻转 2.求两个字符串的最长公共子序列

```python
class Solution(object):
    def longestPalindromeSubseq(self, s):
        # 增加空字符串
        s1 = "0" + s
        s2 = "0" + s[::-1]

        n = len(s1)

        # s1[0...i][0...j]的最长公共子序列的长度
        dp = [[0 for _ in range(n)] for _ in range(n)]

        for i in range(n):
            dp[0][i] = 0
            dp[i][0] = 0

        for i in range(1, n):
            for j in range(1, n):
                if s1[i] == s2[j]:
                    dp[i][j] = dp[i - 1][j - 1] + 1
                else:
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])

        return dp[-1][-1]
```


### 2.2.2. 最长回文子串

#### 2.2.2.1. 😭 [5] 最长回文子串


```python
# 中心扩散法
class Solution(object):
    def longestPalindrome(self, s):
        n = len(s)

        maxLen = 0
        ans = ""

        # 奇数(以center为中心, 向两边扩展)
        for center in range(n):  # 分别以每个元素为center
            l, r = center, center
            while l >= 0 and r < n and s[l] == s[r]:  # 向两边扩展的循环条件
                if r - l + 1 > maxLen:
                    maxLen = r - l + 1
                    ans = s[l : r + 1]
                l -= 1
                r += 1

        # 偶数(以center, center+1为中心, 向两边扩展)
        for center in range(n):
            l, r = center, center + 1
            while l >= 0 and r < n and s[l] == s[r]:
                if r - l + 1 > maxLen:
                    maxLen = r - l + 1
                    ans = s[l : r + 1]
                l -= 1
                r += 1

        return ans
```


#### 2.2.2.2. 😭 [131] 分割回文串 ----- 不会呀~

给你一个字符串 s，请你将 s 分割成一些子串，使每个子串都是 回文串 。返回 s 所有可能的分割方案。

示例：

输入：s = "aab"

输出：[["a","a","b"],["aa","b"]]


解法: 动态规划+递归回溯

```
以aab为例，我们dfs的流程是：
    添加"a"--->
    继续添加"a"--->
    继续添加"b"--->
    继续dfs发现i==len越界，将答案["a","a","b"]加入到ret里然后返回-->
    在res中删除最后添加的"b"，并且发现当前层dfs的for循环不能再执行了于是自然返回--->
    res继续删除末尾的"a",再次for循环更长的回文串，但发现"ab"不是回文串，而且for循环执行不下去了遂返回--->
    res继续删除末尾的"a"(空了)，然后for循环(i,j)从(0,0)变为(0,1)，将对应回文串"aa"添加--->
    继续添加"b"-->继续dfs发现i==len越界，将答案["aa", "b"]加入到ret然后返回--->
    在res中删除最后添加的"b"，发现for不能执行了自然返回--->
    res继续删除"aa"，再次for循环找更长的回文串，(i,j)从(0,1)变成了(0,2)，但"子串aab"不是回文串---->
    继续for循环，发现j==len越界，for循环结束，最外层调用的dfs函数自然返回。主函数返回结果集ret...
```

```python
class Solution(object):
    def partition(self, s):
        n = len(s)

        dp = [[0 for _ in range(n)] for _ in range(n)]  # dp[i][j]: s[i...j]子串是否为回文

        # 奇数(以center为中心, 向两边扩展)
        for center in range(n):  # 分别以每个元素为center
            l, r = center, center
            while l >= 0 and r < n and s[l] == s[r]:  # 向两边扩展的循环条件
                dp[l][r] = 1
                l -= 1
                r += 1

        # 偶数(以center, center+1为中心, 向两边扩展)
        for center in range(n):
            l, r = center, center + 1
            while l >= 0 and r < n and s[l] == s[r]:
                dp[l][r] = 1
                l -= 1
                r += 1

        # 递归回溯
        ans = []
        def dfs(land, start):
            if start == n:  # 递归终止条件: 当start遍历到最后一个元素
                ans.append(land[:])
                return
            for i in range(start, n):
                if dp[start][i]:
                    land.append(s[start:i+1])
                    dfs(land, i+1)
                    land.pop(-1)

        dfs([], 0)
        return ans
```

#### 2.2.2.3.  😭 [132] 分割回文串 II ----- 不会呀~

给定一个字符串，要求将字符串划分若干段，每一段都是回文串，求最少的划分次数

解答：问题其实很简单

> f[i]表示str[0，i-1]最少可以划分成几个回文串
>
> 动态转移方程：str被划分成2段
>
> > ①前面一段，已经被计算出，直接使用 `dp[i]`
> >
> > ②后面一段应该提前计算出，是否是回文串（采用中心扩散生成回文串的方式构造`dp[j][i]`）
>
>  f[i]=min{ f[i-j], j ∈ [0,i-1]，<u>str[j,i]是回文串, 即 `dp[j][i]==true`</u>}
```python
class Solution(object):
    def minCut(self, s):
        # 划分型DP: dp[i]表示前i个字母(不包含s[i])最少可以划分成几个回文串
        # dp[i] = min{dp[j]}+1, 划分点j∈[0,i-1], 划分点将s[0,i-1]切割成2段, 分别是s[0,j-1], s[j,i-1]
        #                             s[j,i-1]应该是回文

        def plainstr(s):
            size = len(s)
            dp = [[False]*size for _ in range(size)] # dp[i][j]: s[i...j]是否是回文串
            # 奇数
            for c in range(size):
                l,r = c,c
                while l >= 0 and r < size and s[l]==s[r]:
                    dp[l][r] = True
                    l -= 1
                    r += 1
            # 偶数
            for c in range(size):
                l,r = c,c+1
                while l >= 0 and r < size and s[l]==s[r]:
                    dp[l][r] = True
                    l -= 1
                    r += 1
            return dp
        
        MAX_INT = 2**32-1

        if s == "":
            return 0

        size = len(s)
        
        dplr = plainstr(s)
        
        dp = [0 for _ in range(size+1)]  # dp语义: "前"i个, 所以长度是 size+1
        dp[0] = 0  # 初始化: 空串可以被分成0个回文串
        for i in range(1,size+1):
            dp[i] = MAX_INT
            for j in range(i):  # 遍历每一个划分位置
                if dplr[j][i-1] == True:  # 当s[j,i-1]是回文时,尝试更新dp[i]
                    dp[i] = min(dp[j]+1, dp[i])

        # 分割次数 = 回文串数 - 1
        return dp[-1]-1
```




# 3. 序列型

### 3.1. [198] 打家劫舍

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

```python
class Solution(object):
    def rob(self, nums):
        N = len(nums)

        if N == 0:
            return 0
        if N == 1:
            return nums[0]
        if N == 2:
            return max(nums[0], nums[1])

        # dp[i] 在前i个房子中盗窃，获得的最大价值
        # dp[i] = max(偷第i个房子, 不偷第i个房子) = max(dp[i-2]+nums[i], dp[i-1])
        dp = [0 for _ in range(N)]

        dp[0] = nums[0]
        dp[1] = max(nums[1], nums[0])

        for i in range(2, N):
            dp[i] = max(dp[i - 2] + nums[i], dp[i - 1])

        return dp[-1]
```
 
### 3.2. [213] 打家劫舍 II

你是一个专业的小偷，计划偷窃沿街的房屋，每间房内都藏有一定的现金。这个地方所有的房屋都 围成一圈 ，这意味着第一个房屋和最后一个房屋是紧挨着的。同时，相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警 。

给定一个代表每个房屋存放金额的非负整数数组，计算你 在不触动警报装置的情况下 ，能够偷窃到的最高金额。

```python
class Solution(object):
    # 碰到圈的问题: 要进行破圈!
    def rob(self, nums):
        N = len(nums)
        if N == 0:
            return 0
        if N == 1:
            return nums[0]
        if N == 2:
            return max(nums[0], nums[1])

        def robMax(nums): # 这个就是打家劫舍问题I (代码没变化)
            N = len(nums)
            dp = [0 for _ in range(N)]
            dp[0] = nums[0]
            dp[1] = max(nums[0], nums[1])
            for i in range(2, N):
                dp[i] = max(nums[i] + dp[i - 2], dp[i - 1])
            return dp[-1]

        # 由于0和N-1房子是邻居, 所以不能同时偷盗, 因此可以分为以下2种情况
        #    没偷房子第N个房子, 偷的范围 [0, N) --> 0 <= i < n
        #    没偷房子第0个房子, 偷的范围 (0, N] --> 0 < i <= n
        return max(robMax(nums[0:-1]), robMax(nums[1:]))
```



### 3.3. [256] 粉刷房子

假如有一排房子，共 n 个，每个房子可以被粉刷成红色、蓝色或者绿色这三种颜色中的一种，你需要粉刷所有的房子并且使其相邻的两个房子颜色不能相同。

当然，因为市场上不同颜色油漆的价格不同，所以房子粉刷成不同颜色的花费成本也是不同的。每个房子粉刷成不同颜色的花费是以一个 n x 3 的矩阵来表示的。

例如，costs[0][0] 表示第 0 号房子粉刷成红色的成本花费；costs[1][2] 表示第 1 号房子粉刷成绿色的花费，以此类推。请你计算出粉刷完所有房子最少的花费成本。

```python
class Solution(object):
    def minCost(self, costs):
        
        N = len(costs)

        if N == 0:
            return 0
        
        dp = [ [0 for _ in range(N)] for _ in range(3)]

        # dp[i][j] 前i个房子，当第i个房子被刷成j颜色时，最小的花费，0<=j<颜色数

        dp[0][0] = costs[0][0]
        dp[0][1] = costs[0][1]
        dp[0][2] = costs[0][2]

        ans = INT_MAX
        for i in range(1, N):
            dp[i][0] = costs[i][0] + min(dp[i-1][1], dp[i-1][2])
            dp[i][1] = costs[i][1] + min(dp[i-1][0], dp[i-1][2])
            dp[i][2] = costs[i][2] + min(dp[i-1][0], dp[i-1][1])
            ans = min(dp[i][0], dp[i][1], dp[i][2])

        return ans
```

---

# 4.博弈型

### 4.1. [486] 预测赢家: 纸牌博弈

给你一个整数数组 nums 。玩家 1 和玩家 2 基于这个数组设计了一个游戏。

玩家 1 和玩家 2 轮流进行自己的回合，玩家 1 先手。开始时，两个玩家的初始分值都是 0 。每一回合，玩家从数组的任意一端取一个数字（即，nums[0] 或 nums[nums.length - 1]），取到的数字将会从数组中移除（数组长度减 1 ）。玩家选中的数字将会加到他的得分上。当数组中没有剩余数字可取时，游戏结束。

如果玩家 1 能成为赢家，返回 true 。如果两个玩家得分相等，同样认为玩家 1 是游戏的赢家，也返回 true 。你可以假设每个玩家的玩法都会使他的分数最大化。

解1: 暴力递归

```python
class Solution(object):
    def predictTheWinner(self, nums):
        n = len(nums)

        # 先手获取到的数字总和
        def first(l, r):
            # 相等的话，一定是先手拿到所有的数字
            if l == r:
                return nums[l]
            # 先手的人，一定是从下面2种case取较大值
            return max(nums[l] + last(l + 1, r), nums[r] + last(l, r - 1))

        # 后手获取到的数字总和
        def last(l, r):
            # 相等的话，后手一定拿不到数字
            if l == r:
                return 0
            # 后手的人，(因为2者都绝顶聪明)，所以，先手肯定给后手留下下面2种case的较小值
            return min(first(l + 1, r), first(l, r - 1))

        return first(0, n - 1) >= last(0, n - 1)
```

解1: 动态规划

```python
class Solution(object):
    def predictTheWinner(self, nums):
        n = len(nums)
        
        if n%2 == 0:
            return True

        f_dp = [[0 for _ in range(n)] for _ in range(n)]  # f_dp[i][j] 表示[i...j]先手拿到的最大值
        l_dp = [[0 for _ in range(n)] for _ in range(n)]  # l_dp[i][j] 表示[i...j]后手拿到的最大值

        for i in range(n-1, -1, -1):  # for i=n-1; i>=0; i--
            for j in range(i, n):  # for j=i; j<0; j++
                if i == j:
                    f_dp[i][j] = nums[i]
                    l_dp[i][j] = 0
                else:
                    f_dp[i][j] = max(nums[i] + l_dp[i+1][j], nums[j] + l_dp[i][j-1])
                    l_dp[i][j] = min(f_dp[i+1][j], f_dp[i][j-1])

        return f_dp[0][n-1] >= l_dp[0][n-1]
```


### 4.2. [877] 石子游戏 mid

解1：与 `[486] 预测赢家` 解法一样

```python
class Solution(object):
    def stoneGame(self, piles):
        n = len(piles)
        
        if n%2 == 0:
            return True
        
        f_dp = [[0 for _ in range(n)] for _ in range(n)]  # f_dp[i][j] 表示[i...j]先手拿到的最大值
        l_dp = [[0 for _ in range(n)] for _ in range(n)]  # l_dp[i][j] 表示[i...j]后手拿到的最大值

        for i in range(n-1, -1, -1):  # for i=n-1; i>=0; i--
            for j in range(i, n):  # for j=i; j<0; j++
                if i == j:
                    f_dp[i][j] = piles[i]
                    l_dp[i][j] = 0
                else:
                    f_dp[i][j] = max(piles[i] + l_dp[i+1][j], piles[j] + l_dp[i][j-1])
                    l_dp[i][j] = min(f_dp[i+1][j], f_dp[i][j-1])

        return f_dp[0][n-1] >= l_dp[0][n-1]
```

解2：数学推断 --- 偶数个，先手必胜

```python
class Solution(object):
    def stoneGame(self, piles):
        return True
```

### 4.3. [1140] 石子游戏 2 mid

### 4.4. [403] 青蛙过河 hard

### 4.5. [2498] 青蛙过河 2 mid


---

# 5. 股票问题 -- 买卖股票的最佳时机

## 5.1. [121] 买卖股票的最佳时机

## 5.2. [122] 买卖股票的最佳时机 2

## 5.3. [123] 买卖股票的最佳时机 3

## 5.4. [188] 买卖股票的最佳时机 4



---

# 6. 背包型

```
 0-1 背包问题
　　第 416 题：分割等和子集
　　第 474 题：一和零
　　第 494 题：目标和
　　组合总和IV
```


[01背包问题](https://www.acwing.com/problem/content/2/)

```python
# @param: weight 数组   每个背包的重量
# @param: val    数组   每个背包的价值
# @param: n      背包的最大重量
# @return: 背包重量为n，所能装下物品的最大价值
def package01(weight, val, n):
    m = len(weight) # 背包个数

    # dp[i][j] 背包重量为j时，从前i个物品中选，获得的最大价值
    dp = [ [0 for _ in range(n+1)] for _ in range(m+1)]

    for i in range(m): # 背包重量为0
        dp[i][0] = 0
    for i in range(n): # 物品重量为0
        dp[0][i] = 0

    for i in range(1, m):
        for j in range(1, n):
            if j < weight[i]: # 背包能容纳的重量 < 第i个物品的重量
                dp[i][j] = dp[i-1][j]
            else:  # max(不选第i个物品, 选第i个物品)
                dp[i][j] = max(dp[i-1][j], dp[i-1][j-weight[i]]+val[i])

    return dp[-1][-1]
```
