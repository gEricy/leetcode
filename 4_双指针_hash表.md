hot 100
- [283]移动零
- [11] 盛最多水的容器
- [42] 接雨水
- [15] 三数之和


### 1. 删除有序数组中的重复项

#### 1.1. [26] 删除有序数组中的重复项

给你一个 升序排列 的数组 nums ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。

不要使用额外的空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。


```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int j=0;
        for (int i=0; i<nums.size(); i++) {
            if (nums[i] != nums[j]) {
                nums[++j] = nums[i];
            }
        }
        return j+1;
    }
};
````

#### 1.2. 😭[80] 删除有序数组中的重复项 II

给你一个有序数组 nums ，请你 原地 删除重复出现的元素，使得出现次数超过两次的元素只出现两次 ，返回删除后数组的新长度。

不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。

```c++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int size = nums.size();
        if (size < 3) {
            return size;
        }

        int l = 2, r = 2;
        while (r < size) {
            if (nums[l-2] == nums[r]) {
                r++;
            } else {
                nums[l] = nums[r];
                l++;
                r++;
            }
        }
        return l;
    }
};
```

### 2. [27] 移除元素

给你一个数组 nums 和一个值 val，你需要 原地 移除所有数值等于 val 的元素，并返回移除后数组的新长度。

不要使用额外的数组空间，你必须仅使用 O(1) 额外空间并 原地 修改输入数组。

元素的顺序可以改变。你不需要考虑数组中超出新长度后面的元素。

```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int j=0;
        for(int i=0; i<nums.size(); i++) {
            if (nums[i] != val) {
                nums[j++] = nums[i];
            }
        }
        return j;
    }
};
```

### 3. 🔥[283] 移动零

给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

请注意 ，必须在不复制数组的情况下原地对数组进行操作。

 
```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        int j=0;
        for (int i=0; i<nums.size(); i++) {
            if (nums[i] != 0) {
                nums[j++] = nums[i];
            }
        }
        while (j < nums.size()) {
            nums[j++] = 0;
        }
    }
};
```

### 4. [88] 合并两个有序数组

给你两个按 非递减顺序 排列的整数数组 nums1 和 nums2，另有两个整数 m 和 n ，分别表示 nums1 和 nums2 中的元素数目。

请你 合并 nums2 到 nums1 中，使合并后的数组同样按 非递减顺序 排列。

注意：最终，合并后数组不应由函数返回，而是存储在数组 nums1 中。为了应对这种情况，nums1 的初始长度为 m + n，其中前 m 个元素表示应合并的元素，后 n 个元素为 0 ，应忽略。nums2 的长度为 n 。

```c
输入：
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

输出：[1,2,2,3,5,6]
```



```c++
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int size = m+n;

        int k = size-1; // 目标

        int i = m-1;
        int j = n-1;

        while (i>=0 && j>=0) {
            if (nums1[i] > nums2[j]) {
                nums1[k--] = nums1[i--];
            } else {
                nums1[k--] = nums2[j--];
            }
        }
        
        while (j >= 0) {
            nums1[k--] = nums2[j--];
        }
    }
};
```



### 5. 😄[75] 颜色分类 / 荷兰国旗

给定一个包含红色、白色和蓝色、共 n 个元素的数组 nums ，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

必须在不使用库内置的 sort 函数的情况下解决这个问题。

- 三指针: i, l, r
- 交换: (i, l) (i, r)



```c++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int i=0;             // 向后遍历
        int l=0;             // l指向非0元素
        int r=nums.size()-1; // r指向非2元素

        while (i <= r) {
            switch(nums[i]) {
            case 0: // 0换到最左边
                swap(nums[i], nums[l]);
                i++;
                l++;
                break;
            case 1:
                i++;
                break;
            case 2: // 2换到最右边
                swap(nums[i], nums[r]); // r--  i不变
                r--;
                break;
            }
        }
    }
};
```


### 6. 🔥[11] 盛最多水的容器

- 注意点

  >  长度：r-l，不是r-l+1
  >
  > height[l], height[r]：谁小，就放弃谁


- 面积area = (r-l) * min(height[r], height[l])


```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int ans = 0;

        int l=0;
        int r=height.size()-1;

        while (l < r) {
            ans = max(  ans, (r-l) * min(height[l],height[r])  ); // 面积
            if (height[l] > height[r]) {
                r--;
            } else {
                l++;
            }
        }
        return ans;
    }
};
```

### 7. 🔥 [42] 接雨水

给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。


可以说，接雨水，是最简单的“hard”级别的题了

1. 获得最大值数组
    1. 从左向右看的最大值: leftMax[i]
    2. 从右向左看的最大值: rightMax[i]
2. ans += min(leftMax[i], rightMax[i]) - height[i]

```c++
class Solution {
public:
    int trap(vector<int>& height) {
        int n = height.size();

        // 从左向右看，最大值
        vector<int> lMax(n, 0);
        lMax[0] = height[0];
        for (int i=1; i<n; i++) {
            lMax[i] = max(lMax[i-1], height[i]);
        }

        // 从右向左看，最大值
        vector<int> rMax(n, 0);
        rMax[n-1] = height[n-1];
        for(int i=n-2; i>=0; i--) {
            rMax[i] = max(rMax[i+1], height[i]);
        }

        int ans=0;
        for (int i=0; i<n; i++) {
            ans += min(lMax[i], rMax[i]) - height[i]; // 水的高度
        }

        return ans;
    }
};
```

---
---
---


### 8. hash表

#### 🔥[1] 两数之和  

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

- 题目要求：无序数组，只需要返回一组


```python
class Solution(object):
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        hash = {} # {数字，下标}
        for i in range(len(nums)):
            hash[nums[i]] = i
        
        for i in range(len(nums)):
            diff = target-nums[i]
            if diff in hash and hash[diff] != i: # 找到了 && 找到的值不是自身
                return [i, hash[diff]] # 只需要找一组
        
        return [-1,-1]
```

#### 🔥[49] 字母异位词分组

```
示例 1:
输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
输出: [["bat"],["nat","tan"],["ate","eat","tea"]]

解释：
    在 strs 中没有字符串可以通过重新排列来形成 "bat"。
    字符串 "nat" 和 "tan" 是字母异位词，因为它们可以重新排列以形成彼此。
    字符串 "ate" ，"eat" 和 "tea" 是字母异位词，因为它们可以重新排列以形成彼此。
```

hash表的应用

```python
class Solution(object):
    def groupAnagrams(self, strs):
        """
        :type strs: List[str]
        :rtype: List[List[str]]
        """
        hash = {} # {排序后的str, []}

        for s in strs:
            key = "".join(sorted(s))  # 字符串排序
            if key in hash: # 找到，存入结果集
                hash[key].append(s)
            else: # 未找到，初始化结果集
                hash[key] = [s]
        
        return hash.values()
```

#### 🔥 [438]. 找到字符串中所有字母异位词

```
示例 1:
    输入: s = "cbaebabacd", p = "abc"
    输出: [0,6]
    解释:
        起始索引等于 0 的子串是 "cba", 它是 "abc" 的异位词。
        起始索引等于 6 的子串是 "bac", 它是 "abc" 的异位词。
 示例 2:
    输入: s = "abab", p = "ab"
    输出: [0,1,2]
    解释:
        起始索引等于 0 的子串是 "ab", 它是 "ab" 的异位词。
        起始索引等于 1 的子串是 "ba", 它是 "ab" 的异位词。
        起始索引等于 2 的子串是 "ab", 它是 "ab" 的异位词。
```

滑动窗口 + hash表

```python

class Solution(object): # [a,z] 一共26个字符
    def findAnagrams(self, s, p):
        """
        :type s: str
        :type p: str
        :rtype: List[int]
        """
        
        pLen = len(p)
        sLen = len(s)

        if pLen > sLen:
            return []

        def char_index(ch):
            return ord(ch) - ord('a')
        
        count_p = [0] * 26 # 替代hash表: 统计词频个数
        count_s = [0] * 26


        # 初始化窗口
        for i in range(pLen):            
            count_p[char_index(p[i])] += 1
            count_s[char_index(s[i])] += 1
        
        ans = []

        if count_p == count_s:
            ans.append(0)
        
        for i in range(pLen, sLen): 
            count_s[char_index(s[i])] += 1       # 新元素进入窗口(个数+1)
            count_s[char_index(s[i-pLen])] -= 1  # 旧元素弹出窗口(个数-1)
            if count_p == count_s:
                ans.append(i-pLen+1)
        
        return ans
```

#### 🔥【128】最长连续序列

给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 O(n) 的算法解决此问题。

 
```
示例 1：
    输入：nums = [100,4,200,1,3,2]
    输出：4
    解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。

示例 2：
   输入：nums = [0,3,7,2,5,8,4,6,0,1]
   输出：9
   
示例 3：
   输入：nums = [1,0,1,2]
   输出：3
```

**解题关键**
1. 将所有数全部放入hash表
2. 遍历每个数字，如果这个数字的前一个数字不在hash表，说明这个数就是起点 ---> 开始找连续子序列

```python
class Solution(object):
    def longestConsecutive(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        # 1. 用集合去重，实现O(1)查找
        hash = {}
        for num in nums:
            hash[num] = 1
        
        ans = 0
        # 2. 遍历集合中的每个数字
        for num in hash:
            # 关键：如果当前数字的前一个数字num-1不在集合中，说明是序列起点 ---> 才去找num的连续子序列
            if num-1 not in hash:
                # 开始找num的连续子序列
                tLen = 0
                kk = num
                while kk in hash:
                    tLen += 1
                    kk += 1
                ans = max(ans, tLen)

        return ans
```

---
---
---


#### 8.2. [167] 两数之和 II - 输入有序数组

非递减顺序排列，两数之和 = 目标值

```c
输入：numbers = [2,7,11,15], target = 9
输出：[1,2]
解释：2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。返回 [1, 2] 。
```

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int l=0;
        int r=numbers.size()-1;

        while (l<r) {
            int sum = numbers[l]+numbers[r];
            if (sum == target) {
                return vector<int>{l+1, r+1};
            } else if (sum > target) {
                r--;
            } else {
                l++;
            }
        }
        return vector<int>{-1,-1};
    }
};
```



### 9. 🔥[15] 三数之和

给你一个整数数组 nums ，判断是否存在三元组 [nums[i], nums[j], nums[k]] 满足 i != j、i != k 且 j != k ，同时还满足 nums[i] + nums[j] + nums[k] == 0 。请

你返回所有和为 0 且不重复的三元组。

注意：答案中不可以包含重复的三元组。


解题:

1. for循环遍历固定一个数nums[i], 0 <= i <= size-3
2. 固定后半段:l=i+1, r=size-1
3. 获取 nums[i]+nums[l]+nums[r]==0的结果集，保存起来


去重:
1. 去重1：固定的数不为同一个，i>0 and nums[i]==nums[i-1]，如：[-1],-1,0,1，消除多次-1,-1
2. 去重2：当sum=nums[i]+nums[l]+nums[r]=0时，l<r and nums[l]!=nums[l-1]，如：[-1],0,0,0,1。消除多次0,0,0

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> ans;
        int size = nums.size();

        sort(nums.begin(), nums.end()); // 先排序

        for (int i=0; i<size; i++) {
            if (i > 0) { // 去重
                if (nums[i] == nums[i-1]){
                    continue;
                }
            }
            // 固定一个数nums[i]
            int l = i+1;
            int r = size-1;
            while (l < r) {
                int sum = nums[i] + nums[l] + nums[r];
                if (sum == 0) {
                    ans.push_back(vector<int>{nums[i],nums[l],nums[r]});
                    l++;
                    while (l < r && nums[l] == nums[l-1]) { // 去重
                        l++;
                    }
                } else if (sum > 0) {
                    r--;
                } else {
                    l++;
                }
            }
        }

        return ans;
    }
};
```


### 10. 😄 [16] 最接近的三数之和


输入：nums = [-1,2,1,-4], target = 1

输出：2

解释：与 target 最接近的和是 2 (-1 + 2 + 1 = 2) 

这个题目比较简单
1. 不需要做去重处理
2. 只需要，在不相等的时候，更新best
   

```python
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {

        int best_sum = 1e7;

        int size = nums.size();

        sort(nums.begin(), nums.end());

        for (int i=0; i<size; i++) {
            // 固定一个数 nums[i]
            int l = i+1;
            int r = size-1;
            while (l < r) {
                int sum = nums[i] + nums[l] + nums[r];
                if (sum == target) { // 相等，直接返回target
                    return target;
                } else if (sum > target) {
                    if (abs(sum - target) < abs(best_sum - target)) {
                        best_sum = sum;
                    }
                    r--;
                } else {
                    if (abs(sum - target) < abs(best_sum - target)) {
                        best_sum = sum;
                    }
                    l++;
                }
            }
        }

        return best_sum;
    }
};
```

---
---


### 12. [189] 轮转数组

给定一个整数数组 nums，将数组中的元素向右轮转 k 个位置，其中 k 是非负数。

```c
输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右轮转 1 步: [7,1,2,3,4,5,6]
向右轮转 2 步: [6,7,1,2,3,4,5]
向右轮转 3 步: [5,6,7,1,2,3,4]
```

解题：三步翻转

分割规则: 先分k个数组成右部分，剩下的就是左部分

1. 以k为界限，分成左右2部分 `[0,n-k-1]` `[n-k,n-1]`
2. 先将左右2部分分别翻转，再整体翻转

```python
class Solution(object):
    def rotate(self, nums, k):
        def reverse(nums, l, r):
            while l < r:
                nums[l], nums[r] = nums[r], nums[l]
                l += 1
                r -= 1

        n = len(nums)
        k %= n
        reverse(nums, n - k, n-1)
        reverse(nums, 0, n - k-1)
        reverse(nums, 0, n - 1)
```

