
- 优先级: 比较运算符 > 位运算符

- mask = (1 << i)

# 1、 x &= (x-1)  将x最右边的1变为0，其他位不变

> ```shell
>     5 ---> 0101
>     4 ---> 0100
> ------------------
>            0100   ---- 最右边的1变为0
> ```


## 1.1. [191] 位1的个数

```c++
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int cnt = 0;
        while (n) {
            n &= n-1;
            cnt++;
        }
        return cnt;
    }
};
```

## 1.2. [461] 汉明距离

```c++
class Solution {
public:
    int hammingDistance(int x, int y) {
        int m = x^y;

        int cnt = 0;
        while (m) {
            m &= m-1;
            cnt++;
        }
        return cnt;
    }
};
```

## 1.3. [477] 汉明距离总和


计算一个`数组`中，任意两个数之间汉明距离的总和。


```c++
class Solution {
public:
    int totalHammingDistance(vector<int>& nums) {
        
        int cnt = 0;

        for (int i=0; i<32; i++) { // 遍历每一个bit位
            int mask = 1<<i;

            int zero = 0;
            int one = 0;
            
            for (auto num : nums) { // 统计数组nums，该bit位是0和1的个数
                if((num & mask) != 0) {
                    one++;
                } else {
                    zero++;
                }
            }

            cnt += one * zero;
        }
        
        return cnt;
    }
};
```

```python
class Solution(object):
    def totalHammingDistance(self, nums):
        ans = 0
        for i in range(32):
            zero = 0
            one = 0
            mask = 1 << i
            for num in nums:
                if num & mask == 0:
                    zero += 1
                else:
                    one += 1
            ans += zero * one
        return ans
```



## 1.4. [231] 2的幂

一个数 n 是 2 的幂，当且仅当 n 是正整数，并且 n 的二进制表示中仅包含 1 个 1。

因此，该问题转化为，将 n 的二进制表示中最低位的那个 1 提取出来，再判断剩余的数值是否为 0 即可。

```c++
class Solution {
public:
    bool isPowerOfTwo(int n) {
        return n>0 && ((n&(n-1)) == 0);
    }
};
```

```python
class Solution(object):
    def isPowerOfTwo(self, n):
        return n > 0 and n & (n - 1) == 0
```

## 1.5. [342] 4的幂

```c++
class Solution {
public:
    bool isPowerOfFour(int n) {
        return n>0 && ((n&(n-1)) == 0) && (n%3 == 1);
    }
};
```

```python
class Solution(object):
    def isPowerOfFour(self, n):
        return n > 0 and n & (n - 1) == 0 and n % 3 == 1
```

# 2、 异或 、 &mask 、 lowbit

`lowbit`:  x &= (-x)   获取x最低bit位的那个1

> ```shell
>     5 ---> 0000 0000 0000 0101
>     
>     负数的二进制: 负数原码, 除符号位取反, 再+1
>     -5---> 1000 0000 0000 0101   (1.原码) 最高位是符号位, 负数为1，正数为0
>            1111 1111 1111 1010   (2.除最高符号位, 其他位取反)
>     -----------------------------
>            1111 1111 1111 1011   (3.再+1)
>            
>     5 & (-5) = 0000 0000 0000 0001
>            0000 0000 0000 0101
>            1111 1111 1111 1011
>     -----------------------------
>            0000 0000 0000 0001
>     结论:
>       x & (-x) = 保留x最右边的1, 其他位抹0
> ```

`异或操作`

> 0 ^ 0 = 0
> 
> 1 ^ 0 = 1  ,   0 ^ 1 = 1
> 
> 1 ^ 1 = 0
> 
> 0 ^ x = x
> 
> x ^ x = 0



`nums[i] & mask`

> ```c++
> int mask = (1 << i);
> if ( (nums[i] & mask) != 0) {
>     /* 不为1 */ 
> }
> ```
 

## 2.1. [136] 只出现一次的数字

```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int xx = 0;
        for (auto num : nums) {
            xx ^= num;
        }
        return xx;
    }
};
```

```python
class Solution(object):
    def singleNumber(self, nums):
        ans = 0
        for num in nums:
            ans ^= num
        return ans
```

## 2.2. [137] 只出现一次的数字 II

给你一个整数数组 nums ，除某个元素仅出现 `一次` 外，其余每个元素都恰出现 `三次`。 请你找出并返回那个只出现了一次的元素。

```c++
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        
        int ans = 0;

        for (int i=0; i<32; i++) {
            int mask = (1 << i);
            int bit = 0; // 该bit位为1的个数
            for (auto num : nums) {
                if ( (num & mask) != 0) {
                    bit++;
                }
            }
            if (bit % 3 != 0) {
                ans += mask;
            }
        }

        return ans;
    }
};
```

```python
```

## 2.3. [260] 只出现一次的数字 III

给你一个整数数组 nums，其中恰好有两个元素只出现 一次，其余所有元素均出现 两次。 找出只出现一次的那两个元素。你可以按 任意顺序 返回答案。

解题：

1. 遍历数组，执行异或操作，得到 xx = x1^x2
2. 计算 lowbit = xx & (-xx)
3. 采用 lowbit 将数组分成2组：① 对于任意一个在数组 nums 中出现两次的元素，该元素的两次出现会被包含在同一类中；② 对于任意一个在数组 nums 中只出现了一次的元素，即 x1 和 x2，它们会被包含在不同类中
4. 因此，将这2组的元素分别异或起来，就得到 x1 和 x2


```c++
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        unsigned int xx = 0; // 遍历数组，逐一异或，得到 xx = x1 ^ x2
        for (auto num : nums) {
            xx ^= num;
        }

        int lowbit = xx & (-xx); // 保留xx的最右边的1，其他位置全部抹0

        int a=0, b=0;
        for (auto num : nums) {
            if ( (lowbit & num) != 0) { // 使用lowbit将num分组: 相同的元素一定分到一个组，x1和x2一定分在不同的组
                a ^= num;
            } else {
                b ^= num;
            }
        }
        return {a, b};
    }
};
```

```python
class Solution(object):
    def singleNumber(self, nums):
        xx = 0
        for num in nums:
            xx ^= num

        lowbit = xx & (-xx)

        a = 0
        b = 0
        for num in nums:
            if num & lowbit == 0:
                a ^= num
            else:
                b ^= num
        return [a, b]
```

### 2.4. [190] 颠倒二进制位


```c++
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        
        int ans = 0;

        for (int i=0; i<32; i++) {
            int mask = 1 << i;
            if ( (mask & n) != 0 ) {  // 当前bit位(i)不为0, 对称bit位(31-i)设为1
                ans += 1<<(31-i);
            }
        }
        
        return ans;
    }
};
```

```python
class Solution:
    def reverseBits(self, n):
        ans = 0
        for i in range(32):
            mask = 1 << i
            reverseMask = 1 << (31 - i)
            if mask & n != 0:
                ans += reverseMask
        return ans
```

# 3. 小技巧



## 3.1. [201] 数字范围按位与


```c++
给定范围 [m, n]，其中 0 <= m <= n <= 2147483647，返回此范围内所有数字的按位与（包含 m, n 两端点）。
输入: [5,7]   # 即, 5 & 6 & 7
输出: 4
```


解题：该问题转换为求公共前缀

```c++
class Solution {
public:
    int rangeBitwiseAnd(int left, int right) {
        
        int cnt = 0;
        while (left < right) {
            left >>= 1;
            right >>= 1;
            cnt++;
        }

        return left << cnt;
    }
};
```

```python
class Solution(object):
    def rangeBitwiseAnd(self, left, right):
        cnt = 0
        while left < right:
            left >>= 1
            right >>= 1
            cnt += 1
        return left << cnt  # return right << cnt
```

## 3.2. [371] 两整数之和


> bit位相加, 不进位: a^b
> 
> 进位: (unsigned int)(a&b) << 1


```c++
class Solution {
public:
    int getSum(int a, int b) {
        int low = a ^ b;
        int high = (unsigned int)(a&b) << 1; // 左移可能会导致溢出
        if (high == 0) { // 递归终止条件: 进位是0
            return low;
        }
        return getSum(low, high);
    }
};
```




## 3.3. [面试题 16.07] 较大数值

编写一个方法，找出两个数字`a`和`b`中最大的那一个。不得使用if-else或其他比较运算符。
