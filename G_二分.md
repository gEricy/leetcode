
# 1. 常规

😄【模板】要求每个元素nums[i]都被定位到，因此，循环条件是while(l <= r)


- 循环条件: while l<=r
- 缩小区间: l = mid+1, r = mid-1


### 1.1. [704] 二分查找

```python
class Solution(object):
    def search(self, nums, target):
        l, r = 0, len(nums)-1
        while l <= r:
            mid = (l+r)/2
            if nums[mid] == target:
                return mid
            elif nums[mid] < target:
                l = mid+1
            else:
                r = mid-1
        return -1
```



### 1.2. [74] 搜索二维矩阵

编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：

1. 每行中的整数从左到右按升序排列
2. 每行的第一个整数大于前一行的最后一个整数

答案：解题思路，就是最简单的二分查找，只不过需要进行坐标的转换 **mid = \[mid/n\]\[mid%n\]**

```python
[
  [ 1, 3, 5, 7],
  [10,11,16,20],
  [23,30,34,50]
]
```

```python
class Solution(object):
    def searchMatrix(self, matrix, target):
        m = len(matrix)     # m行n列
        n = len(matrix[0])

        l, r = 0, m*n-1

        while l <= r:
            mid = (l+r) / 2
            if matrix[mid / n][mid % n] == target:  # mid ==> (mid/n, mid%n)
                return True
            elif matrix[mid / n][mid % n] > target:
                r = mid-1
            else:
                l = mid+1

        return False
```


### 1.3. [240] 搜索二维矩阵 II

备注：该题不是二分查找，与上题呼应，因此放在此处。

1. 每行的元素从左到右升序排列

2. 每列的元素从上到下升序排列

```python
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

```python
class Solution(object):
    def searchMatrix(self, matrix, target):
        m = len(matrix)
        n = len(matrix[0])

        # 从左下角开始搜索
        i = m-1
        j = 0

        while i >= 0 and j <= n-1:
            if matrix[i][j] == target:
                return True
            elif matrix[i][j] > target:
                i -= 1
            else:
                j += 1

        return False
```



# 2. 模板

俗话说的好，二分查找，思路简单，但是一写就废！二分查找成为了众leetcode大佬的难题，但是，下面我就教你怎么用【一套模板】写好二分查找！

😄【模板】

- 循环条件: while l+1 < r:
- 缩小区间: l、r都只变更为mid
- 当不满足while循环条件时，<u>**l、r的状态一定是l+1>=r（此时，只剩下两个位置l, r）**</u>，要判断l、r是否满足条件
  - if nums[l] 满足条件 ？
  - if nums[r] 满足条件 ？

---

## 2.1. 普通排序数组

### 2.1.1. [34] 在排序数组中查找元素的第一个和最后一个位置

```python
class Solution(object):
    def searchRange(self, nums, target):
        size = len(nums)
        if size == 0:
            return [-1, -1]

        def searchFirst():
            l, r = 0, size-1
            while l+1 < r:
                mid = (l+r)/2
                if nums[mid] == target: # 差异点
                    r = mid
                elif nums[mid] > target:
                    r = mid
                else:
                    l = mid
            if nums[l] == target:
                return l
            if nums[r] == target:
                return r
            return -1

        def searchLast():
            l, r = 0, size-1
            while l+1 < r:
                mid = (l+r)/2
                if nums[mid] == target: # 差异点
                    l = mid
                elif nums[mid] > target:
                    r = mid
                else:
                    l = mid
            if nums[r] == target:
                return r
            if nums[l] == target:
                return l
            return -1

        return [searchFirst(), searchLast()]
```

### 2.1.2. [35] 搜索插入位置

给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

请必须使用时间复杂度为 O(log n) 的算法。

```python
class Solution(object):
    def searchInsert(self, nums, target):
        l, r = 0, len(nums)-1
        while l+1 < r:
            mid = (l+r)/2
            if nums[mid] == target:
                r = mid
            elif nums[mid] > target:
                r = mid
            else:
                l = mid
                
        if target <= nums[l]:
            return l
        if target <= nums[r]:
            return r
        return r+1
```

## 2.2. 旋转排序数组

单调递增数组
```
        *
      *
    *
  *
*
```

做了旋转：
```
  *
*
        *
      *
    *
```

### 2.2.1. 😄[33] 搜索旋转排序数组


1. 先锁定单调区间（mid将l,r分成2个区间后，至少会存在一个单调区间）
    - nums[l] < nums[mid]     [l,mid]是单调区间
    - nums[mid] < nums[r]     [mid,r]是单调区间
2. 根据target“在/不在”单调区间内，又分为2种情况
    - [l,mid]是单调区间，
        - target“在”该单调区间内
        - target“不在”该单调区间内
    - [mid,r]是单调区间，
        - target“在”该单调区间内
        - target“不在”该单调区间内

```python
class Solution(object):
    def search(self, nums, target):
        l, r = 0, len(nums)-1
        while l+1 < r:
            mid = (l+r)/2
            if target == nums[mid]:
                return mid
            if target == nums[l]:
                return l
            if target == nums[r]:
                return r
            # 锁定单调区间
            if nums[l] < nums[mid]:
                if nums[l] < target < nums[mid]:
                    r = mid
                else:
                    l = mid
            else:
                if nums[mid] < target < nums[r]:
                    l = mid
                else:
                    r = mid

        if target == nums[l]:
            return l
        if target == nums[r]:
            return r

        return -1
```

### 2.2.2. 😄 寻找旋转排序数组中的最小值

下面2个题目，答案是一个

- `升序数组：已知一个长度为 n 的数组，预先按照升序排列，经由 1 到 n 次 旋转 后，得到输入数组`


[153] 寻找旋转排序数组中的最小值 --- 元素互不相同

```python
class Solution(object):
    def findMin(self, nums):
        if len(nums) == 0:
            return 0
        if len(nums) == 1:
            return nums[0]

        l, r = 0, len(nums)-1
        while l+1 < r:
            mid = (l+r)/2
            if nums[mid] <= nums[r]:
                r = mid
            else:
                l = mid
        return min(nums[l], nums[r])
```

[154] 寻找旋转排序数组中的最小值 II --- 元素可能相同

```python
class Solution(object):
    def findMin(self, nums):
        if len(nums) == 0:
            return 0
        if len(nums) == 1:
            return nums[0]

        l, r = 0, len(nums)-1
        while l+1 < r:
            if nums[r] == nums[r-1]:  # +++
                r -= 1
                continue
            if nums[l] == nums[l+1]:  # +++
                l += 1
                continue
            mid = (l+r)/2
            if nums[mid] <= nums[r]:
                r = mid
            else:
                l = mid
        return min(nums[l], nums[r])
```

## 2.3. 变种题


### 2.3.1. [162]. 寻找峰值

给定一个输入数组 `nums`，其中 `nums[i] ≠ nums[i+1]`，返回任何一个峰值元素的索引。


解题思路: 相邻的两个元素 `nums[mid],nums[mid-1]` 或 `nums[mid],nums[mid+1]`，判断是否出现逆序


```python
class Solution(object):
    def findPeakElement(self, nums):
        l,r = 0,len(nums)-1
        while l+1<r:
            mid=(l+r)/2
            if nums[mid] > nums[mid+1]:
                r=mid
            else:
                l=mid
        return l if nums[l]>nums[r] else r
```



### 2.3.2. 😄[69] x 的平方根 

```python
class Solution(object):
    def mySqrt(self, x):
        l, r = 0, x//2+1 # r的最大值不超过(x//2+1)

        while l+1 < r:
            mid = (l+r)/2
            xx = mid*mid
            if xx == x:
                return mid
            elif xx > x:
                r = mid
            else:
                l = mid

        # 答案是{l,r}中的一个
        return l if r*r > x else r
```



### 2.3.3. 😄[50] Pow(x, n)

快速幂

```python
		x^(n/2) * x^(n/2) * x   # 奇数
x^n = 
		x^(n/2) * x^(n/2)       # 偶数
    
其中, base = x^(n/2), 可以用quickPow(x,n/2)求得
```

注意：当指数n小于0时，要处理底数x，使其变成1/x



```python
class Solution(object):
    def pow(self, x, n):
        if n == 0:
            return 1
        base = pow(x, n//2) # 先求出base
        if n % 2 == 0:  # 偶数
            return base * base
        else:           # 奇数
            return base * base * x

    def myPow(self, x, n):
        x = 1/x if n < 0 else x  # 幂次n<0, 底数取倒数
        n = abs(n)
        return self.pow(x, n)
```

---

### 2.3.4. 😄[4] 寻找两个正序数组的中位数  hard

给定两个大小为 m 和 n 的正序（从小到大）数组 nums1 和 nums2。请你找出并返回这两个正序数组的中位数。

进阶：你能设计一个时间复杂度为 O(log (m+n)) 的算法解决此问题吗？

- **二分 + 分治**

分析：O(log (m+n)) ==> 二分

```

```



