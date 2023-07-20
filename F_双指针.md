### 1. 删除有序数组中的重复项

1.1. [26] 删除有序数组中的重复项

给你一个 升序排列 的数组 nums ，请你 原地 删除重复出现的元素，使每个元素 只出现一次 ，返回删除后数组的新长度。元素的 相对顺序 应该保持 一致 。

不要使用额外的空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成。


```C++
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int j = 0;
        for (int i=1;i<nums.size();i++) {
            if(nums[i] != nums[j]) {
                nums[++j] = nums[i];
            }
        }
        return j+1;
    }
};
````

1.2. [80] 删除有序数组中的重复项 II

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
        for (int i=0; i<nums.size(); i++) {
            if (nums[i] != val) {
                nums[j++] = nums[i];
            }
        }
        return j;
    }
};
```

### 3. [283] 移动零

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
        for (; j<nums.size(); j++) {
            nums[j] = 0;
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
        int k = m+n-1;
        int i = m-1, j = n-1;
        while (i>=0 && j>=0) {
            if (nums1[i] > nums2[j]) {
                nums1[k--] = nums1[i--];
            } else {
                nums1[k--] = nums2[j--];
            }
        }
        while (j>=0) {
            nums1[k--] = nums2[j--];
        }
    }
};
```

### 5. 😄[75] 颜色分类 / 荷兰国旗

给定一个包含红色、白色和蓝色、共 n 个元素的数组 nums ，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

必须在不使用库内置的 sort 函数的情况下解决这个问题。

```c++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int l=0, r=nums.size()-1;
        int i=0;
        while (i <= r) {
            switch (nums[i]) {
                case 0:
                    swap(nums[i++], nums[l++]);
                    break;
                case 1:
                    i++;
                    break;
                case 2:
                    swap(nums[i], nums[r--]);
                    break;
            }
        }
    }
};
```


### 6. [11] 盛最多水的容器

- 注意点

  >  长度：r-l，不是r-l+1
  >
  > height[l], height[r]：谁小，就放弃谁


- 面积area = (r-l) * min(height[r], height[l])

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int l = 0, r = height.size()-1;

        int maxArea = 0;
        while (l < r) {
            maxArea = max(maxArea, (r-l) * min(height[r], height[l]));
            if (height[l] > height[r]) {
                r--;
            } else {
                l++;
            }
        }

        return maxArea;
    }
};
```

### 7. 😄 [42] 接雨水

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
        
        vector<int> lMax(n, 0);
        vector<int> rMax(n, 0);

        // 从左向右看，最大值
        lMax[0] = height[0];
        for (int i=1; i<n; i++) {
            lMax[i] = max(height[i], lMax[i-1]);
        }
        // 从右向左看，最大值
        rMax[n-1] = height[n-1];
        for (int i=n-2; i>=0; i--) {
            rMax[i] = max(height[i], rMax[i+1]);
        }

        int ans = 0;
        for (int i=0; i<n; i++) {
            ans += min(lMax[i], rMax[i]) - height[i];
        }

        return ans;
    }
};
```


### 8. 两数之和

8.1. [1] 两数之和

给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 target  的那 两个 整数，并返回它们的数组下标。

```python
class Solution(object):
    def twoSum(self, nums, target):
        hash = {}  # hash表中存放的是(元素, 下标)，即: (nums[i], i)
        for i in range(len(nums)):
            diff = target - nums[i]
            if diff in hash:
                return [i, hash[diff]]
            else:
                hash[nums[i]] = i
        return [-1, -1]
```

```python
class Solution(object):
    def twoSum(self, nums, target):
        hash = {}  # hash表中存放的是(diff,下标)，即: (target-nums[i], i)
        for i in range(len(nums)):
            if nums[i] in hash:
                return [i, hash[nums[i]]]
            else:
                hash[target - nums[i]] = i
        return [-1, -1]
```

8.2. [167] 两数之和 II - 输入有序数组

非递减顺序排列，两数之和 = 目标值

```c
输入：numbers = [2,7,11,15], target = 9
输出：[1,2]
解释：2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。返回 [1, 2] 。
```

```python
class Solution(object):
    def twoSum(self, numbers, target):
        l,r = 0,len(numbers)-1
        while l<r:
            sum = numbers[l]+numbers[r]
            if sum == target:
                return [l+1,r+1]
            elif sum > target:
                r-=1
            else:
                l+=1
        return [-1,-1]
```

### 9. 😄 [15] 三数之和

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

```python
class Solution(object):
    def threeSum(self, nums):
        n = len(nums)
        ans = []

        nums.sort()

        # 固定一个值nums[i]
        for i in range(n - 2):
            if i > 0 and nums[i] == nums[i - 1]:  # 1.去重
                continue
            # 双指针[l,r]锁定后面的区间
            l = i + 1
            r = n - 1
            while l < r:
                sum = nums[i] + nums[l] + nums[r]
                if sum == 0:
                    ans.append([nums[i], nums[l], nums[r]])
                    l += 1 # 相等时，l要++，继续寻找下一个符合条件的元素
                    while l < r and nums[l] == nums[l - 1]:  # 2.去重
                        l += 1
                elif sum > 0:
                    r -= 1
                else:
                    l += 1

        return ans
```

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        
        vector<vector<int>> ans;
        
        int size = nums.size();

        if (size < 3) {
            return ans;
        }

        sort(nums.begin(), nums.end());

        // 固定一个值nums[i]
        for (int i=0; i < size-2; i++) {
            if (i>0 && nums[i] == nums[i-1]) { // 去重
                continue;
            }
            // 固定两个头尾指针l,r = [i+1, size)
            int l = i+1;
            int r = size-1;
            while (l < r) {
                int sum = nums[i] + nums[l] + nums[r];
                if (sum == 0) {
                    ans.push_back(vector<int>{nums[i], nums[l], nums[r]});
                    l++; // 相等时，l要++，继续寻找下一个符合条件的元素
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


### 11. 😄 [18] 四数之和

给你一个由 n 个整数组成的数组 nums ，和一个目标值 target 。请你找出并返回满足下述全部条件且不重复的四元组 [nums[a], nums[b], nums[c], nums[d]] （若两个四元组元素一一对应，则认为两个四元组重复）：

- 0 <= a, b, c, d < n
- a、b、c 和 d 互不相同
- nums[a] + nums[b] + nums[c] + nums[d] == target

你可以按 任意顺序 返回答案 。


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

1. 右部分: [size-k, size-1]   k个数
2. 左部分: [0, size-k-1]

```c++
class Solution {
public:
    void reverse(vector<int>& nums, int left, int right) {
        while (left < right) {
            swap(nums[left++], nums[right--]);
        }
    }

    void rotate(vector<int>& nums, int k) {
        int size = nums.size();

        k = k % size;

        reverse(nums, size-k, size-1); // 右部分
        reverse(nums, 0, size-k-1);    // 左部分
        reverse(nums, 0, size-1);      // 全部
    }
};
```

