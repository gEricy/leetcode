

# 1. xT = xT * 10 + x % 10

### 1.1. [7]整数反转

-123变为-321

```c
class Solution {
public:
    int reverse(int x) {
        int sign = x >= 0 ? 1 : -1;
        x = abs(x);
        int xx = 0;
        while (x > 0) {
            // 一定要判断是否越界
            if (xx < INT_MIN / 10 || xx > INT_MAX / 10) {
                return 0;
            }
            xx = xx * 10 + x % 10;
            x /= 10;
        }
        return xx * sign;
    }
};
```

### 1.2. [9]回文数

- 不要忘记处理特殊情况: x>0 && x%10==0

该解法，既高效，又可以避免整数溢出的风险

```python
class Solution(object):
    def isPalindrome(self, x):
        if x > 0 and x % 10 == 0:
            return False

        xx = 0
        while x > xx:
            xx = xx * 10 + x % 10
            x /= 10
            
        return True if x == xx else xx / 10 == x
```

# 2.字符串

### 2.1. 😄 [43] 字符串相乘

[解题思路](https://leetcode.cn/problems/multiply-strings/solution/you-hua-ban-shu-shi-da-bai-994-by-breezean/)

nums1[i] 与 nums[j] 相乘，得到的结果tmp，位数如果是2位，那么：

1. 高位(十位): ans[i+j]
2. 低位(个位): ans[i+j+1]


```c++
class Solution {
public:
    string multiply(string num1, string num2) {
        int m = num1.size();
        int n = num2.size();

        vector<int> ans(m+n, 0); // 乘积的最多位数 <= (m+n)

        // 从个位数开始逐位相乘
        for (int i=m-1; i>=0; i--) {
            for (int j=n-1; j>=0; j--) {
                int sum = ans[i+j+1] + (num1[i] - '0') * (num2[j] - '0');
                ans[i+j+1] = sum % 10;  // 低位
                ans[i+j] += sum / 10;   // 高位
            }
        }

        // 去除左边的0: "0000123" ---> "123"
        int i = 0;
        for (; i<ans.size() && ans[i]==0; i++){}

        // 构造结果
        string s;
        for (; i<ans.size(); i++) {
            s += ans[i] + '0';
        }
        
        return s == "" ? "0" : s;
    }
};
```



```python
class Solution(object):
    def multiply(self, num1, num2):
        m = len(num1)
        n = len(num2)

        vec = [0 for _ in range(m + n)]

        for i in range(m - 1, -1, -1):
            for j in range(n - 1, -1, -1):
                sum = vec[i + j + 1] + int(num1[i]) * int(num2[j])
                vec[i + j + 1] = sum % 10
                vec[i + j] += sum / 10

        ans = ""
        for v in vec:
            ans += str(v)

        ans = ans.lstrip("0")

        return "0" if ans == "" else ans
```

--- 

# 3.基本计算器

### 3.1. 😄[224] 基本计算器

给你一个字符串表达式 s ，请你实现一个基本计算器来计算并返回它的值。

注意:不允许使用任何将字符串作为数学表达式计算的内置函数，比如 eval() 。



### 3.2. 😄[227] 基本计算器 II

输入：s = " 3+5 / 2 "

输出：5

```python
class Solution:
    def calculate(self, s: str) -> int:
        stack = []

        cur_num = 0      # 当前数
        last_sign = '+'  # 上一个运算符

        s += '+' # 末尾补充一个+（是为了最后一个数字也能参与计算）
        
        for ch in s:
            if '0' <= ch <= '9': # 数字
                cur_num = cur_num * 10 + (int(ch) - int('0'))
            if ch in '+-/*': # 运算符号
                # 1.加减: 直接放入结果集   乘除: 弹出栈顶元素，和当前数运算完成后，重新放入栈顶
                if last_sign == '+':
                    stack.append(cur_num)
                if last_sign == '-':
                    stack.append(-cur_num)
                if last_sign == '*':
                    stack[-1] = stack[-1] * cur_num
                if last_sign == '/':
                    stack[-1] = int(stack[-1] / cur_num) # python3, int()向上取整
               # 2.重置
                cur_num = 0
                last_sign = ch
        
        return sum(stack) # 返回值 = 累加栈中的值
```


