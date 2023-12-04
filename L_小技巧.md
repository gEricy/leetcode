# 1、二维数组

### 1.1. [48] 旋转图像

- 给定一个 *n* × *n* 的二维矩阵表示一个图像（正方形）

```shell
给定 matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],

原地旋转输入矩阵，使其变为:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```


```c++
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        
        // 上下翻转
        for (int i=0; i<m/2; i++) {
            for (int j=0; j<n; j++) {
                swap(matrix[i][j], matrix[m-i-1][j]);
            }
        }
        // 对角线翻转
        for (int i=0; i<m; i++) {
            for (int j=0; j<i; j++) {
                swap(matrix[i][j], matrix[j][i]);
            }
        }
    }
};
```

### 1.2. [54] 螺旋矩阵

给定一个包含 *m* x *n* 个元素的矩阵（*m* 行, *n* 列），请按照顺时针螺旋顺序，返回矩阵中的所有元素。


```shell
输入:
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
输出: [1,2,3,6,9,8,7,4,5]
```

```c++
class Solution {
public:
    // 解: 就是循环遍历四条边
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();

        int up = 0, down = m-1;
        int left = 0, right = n-1;

        vector<int> ans;

        while (1) {
            // 左到右
            for (int i=left; i<=right; i++) {
                ans.push_back(matrix[up][i]);
            }
            up++;
            if (up > down) {
                break;
            }
            // 上到下
            for (int i=up; i<=down; i++) {
                ans.push_back(matrix[i][right]);
            }
            right--;
            if (left > right) {
                break;
            }
            // 右到左
            for (int i=right; i>=left; i--) {
                ans.push_back(matrix[down][i]);
            }
            down--;
            if (up > down) {
                break;
            }
            // 下到上
            for (int i=down; i>=up; i--) {
                ans.push_back(matrix[i][left]);
            }
            left++;
            if (left > right) {
                break;
            }
        }

        return ans;
    }
};
```

---


# 2. 摩尔投票法

> 1. 投票阶段 + 抵消阶段
>    1. 若候选人存在，票数+1
>    2. 若候选人不存在
>       1. (当候选人存在K-1个)抵消其他候选人
> 2. 遍历剩下的元素，找寻符合条件的结果


### 2.1. [169] 多数元素

给定一个大小为 n 的数组 nums ，返回其中的多数元素。多数元素是指在数组中出现次数 大于 ⌊ n/2 ⌋ 的元素。


```c++
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        int cand = -1; // 候选者
        int cnt = 0;   // 出现次数
        for (auto num : nums) {
            if (cnt == 0) {
                cand = num;
                cnt++;
            } else {
                if (cand == num) {
                    cnt++;
                } else {
                    cnt--;
                }
            }
        }
        return cand;
    }
};
```

### 2.2. 😄 [229] 多数元素 II 

给定一个大小为 n 的整数数组，找出其中所有出现超过 ⌊ n/3 ⌋ 次的元素。

```python
class Solution(object):
    def majorityElement(self, nums):
        size = len(nums)
        hash = {}  # 候选集合: {候选人elem, 票数}
        KK = 3
        for elem in nums:
            if elem in hash.keys():  # 是候选
                hash[elem] += 1
            else:  # 不是候选
                if len(hash) == KK-1:  # 当候选的个数有KK-1个
                    for (k, v) in hash.items():  # 用elem抵消掉候选的一个次数
                        hash[k] -= 1
                        if hash[k] == 0:  # 删除k的键值对
                            hash.pop(k)
                else:  # 没集齐K-1个候选, 就累加次数
                    hash[elem] = 1

        hash_cnt = {}
        ans = []
        for elem in nums:
            for (k, v) in hash.items():
                if k == elem and k not in ans:
                    hash_cnt[elem] = hash_cnt.setdefault(elem, 0) + 1
                    if hash_cnt[k] > size/KK:
                        ans.append(k)
        return ans
```


---


# 3. 置换（原地哈希）

😄 假设，数组arr是有 1~N 组成的，x ∈ [1, N]，通过置换，可以将数组arr变成 [1,2,3...,n]

- 下标: 0,1,2,3
- 元素: 1,2,3,4

元素x = 下标i + 1

`核心要点：有效元素 x ∈ [1,n]，通过不断的置换，它一定能放在 下标为x-1的位置，最终的效果是，nums[x-1] = x`


### 3.1. [41] 缺失的第一个正数

```c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = nums.size();

        // 有效元素 x ∈ [1,n]，应该放在nums[x-1]的位置
        for (int i=0; i<n; i++) {
            while (1 <= nums[i] && nums[i] <= n && nums[nums[i] - 1] != nums[i]) {
                swap(nums[i], nums[nums[i]-1]);
            }
        }

        // 寻找第一个不相等的值
        for (int i=0; i<n; i++) {
            if (nums[i] != i+1) {
                return i+1;
            }
        }
        return n+1;
    }
};
```

### 3.2. [442] 数组中重复的数据

```python
给定一个整数数组 a，其中1 ≤ a[i] ≤ n （n为数组长度）, 其中有些元素出现两次而其他元素出现一次。找到所有出现两次的元素。
你可以不用到任何额外空间并在O(n)时间复杂度内解决这个问题吗？

输入:
[4,3,2,7,8,2,3,1]
输出:
[2,3]
```

```c++
class Solution {
public:
    vector<int> findDuplicates(vector<int>& nums) {
        int n = nums.size();
        for (int i=0; i<n; i++) {
            while (1<=nums[i] && nums[i]<=n && nums[i] != nums[nums[i]-1]) {
                swap(nums[i] ,nums[nums[i]-1]);
            }
        }

        vector<int> ans;
        for (int i=0; i<n; i++) {
            if (nums[i] != i+1) {
                ans.push_back(nums[i]);
            }
        }
        return ans;
    }
};
```

### 3.3. [287] 寻找重复数

```python
给定一个包含 n + 1 个整数的数组 nums，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。假设只有一个重复的整数，找出这个重复的数。
输入: [1,3,4,2,2]
输出: 2
```

```c++
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        int n = nums.size();
        for (int i=0; i<n; i++) {
            while (1<=nums[i] && nums[i]<=n && nums[nums[i]-1] != nums[i]) {
                swap(nums[nums[i]-1], nums[i]);
            }
        }
        for (int i=0; i<n; i++) {
            if (nums[i] != i+1) {
                return nums[i];
            }
        }
        return -1;
    }
};
```

---

# 4. list.sort(cmp_func)

- python的类型list，调用sort函数，指定排序规则cmp_func

注意：比较函数
1. 字符串比较: 使用自带的 `cmp()` 函数，return cmp(s1, s2)
2. int比较: return x-y，而不是 return x>y

### 4.1. [56] 合并区间

输入: intervals = [[1,3],[2,6],[8,10],[15,18]]

输出: [[1,6],[8,10],[15,18]]

解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].


```python
class Solution(object):
    def merge(self, intervals):
        def cmp_func(x, y):  # 按照第一个元素[升序]排序
            return cmp(x[0], y[0])

        intervals.sort(cmp_func)

        ans = []
        for iter in intervals:
            if len(ans) == 0:
                ans.append(iter)
            else:
                x1, y1 = ans[-1]
                x2, y2 = iter
                if x2 > y1:
                    ans.append(iter)
                else:
                    if y2 >= y1:
                        ans[-1][1] = y2
        return ans
```


### 4.2 [179] 最大组合数


输入：nums = [3,30,34,5,9]

输出："9534330"


```python
class Solution(object):
    def largestNumber(self, nums):
        def cmp_func(x, y):
            return cmp(str(y)+str(x), str(x)+str(y)) # 字典序[降序]排序

        nums.sort(cmp_func)

        ans = ""
        for e in nums:
            ans += str(e)

        ans = ans.lstrip("0") 
        if ans == "":
            return "0"
        return ans
```

# 5. TODO

[567]字符串的排列 (mid)

给你两个字符串 s1 和 s2 ，写一个函数来判断 s2 是否包含 s1 的排列。如果是，返回 true ；否则，返回 false 。

换句话说，s1 的排列之一是 s2 的 子串 。


常用的技巧：统计每个字母出现的次数，无需使用map<char, int> char2CntMap，直接使用一个int数组 vector<int> cnt(26, 0)

- 字母ch
- 字母的次数保存在数组中的位置: ch-'a'
- 获取字母ch出现的次数: cnt[ch-'a']

```c++
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        int len1 = s1.size(), len2 = s2.size();
        if (len1 > len2) {
            return false;
        }

        vector<int> cnt1(26, 0),cnt2(26, 0);

        for (int i = 0; i < len1; i++) {
            ++cnt1[s1[i] - 'a'];
            ++cnt2[s2[i] - 'a'];
        }
        if (cnt1 == cnt2) {
            return true;
        }

        for (int i = len1; i < len2; i++) {
            ++cnt2[s2[i] - 'a'];
            --cnt2[s2[i - len1] - 'a'];
            if (cnt1 == cnt2) {
                return true;
            }
        }
        return false;
    }
};
```
