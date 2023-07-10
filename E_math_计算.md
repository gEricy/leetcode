

# 1. xT = xT * 10 + x % 10

### 1.1. [7]整数反转

-123变为-321

```c
class Solution {
public:
    int reverse(int x) {
        int flag = x > 0 ? 1 : -1;

        int ans = 0;
        
        x = abs(x);
        while (x > 0) {
            // 一定要判断是否越界
            if (ans < INT_MIN / 10 || ans > INT_MAX / 10) {
                return 0;
            }
            ans = ans * 10 + x % 10;
            x /= 10;
        }

        return ans * flag;
    }
};
```

### 1.2. [9]回文数

- 不要忘记处理特殊情况: x>0 && x%10==0

该解法，既高效，又可以避免整数溢出的风险

```c
class Solution {
public:
    bool isPalindrome(int x) {
        // 特殊数字判断: 大于0 && 末尾数字为0 (一定不是回文数)
        if (x > 0 && x % 10 == 0)
            return false;
        
        int xT = 0;
        while (x > xT) {
            xT = xT * 10 + x % 10;
            x = x / 10;
        }

        return x == xT ? true : xT / 10 == x;
    }
};
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
                int sum = (num1[i] - '0') * (num2[j] - '0') + ans[i+j+1];
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

--- 

# 3.基本计算器

### 3.1. 😄[224] 基本计算器

给你一个字符串表达式 s ，请你实现一个基本计算器来计算并返回它的值。

注意:不允许使用任何将字符串作为数学表达式计算的内置函数，比如 eval() 。



### 3.2. 😄[227] 基本计算器 II

给你一个字符串表达式 s ，请你实现一个基本计算器来计算并返回它的值。

整数除法仅保留整数部分。

你可以假设给定的表达式总是有效的。所有中间结果将在 [-231, 231 - 1] 的范围内。

注意：不允许使用任何将字符串作为数学表达式计算的内置函数，比如 eval() 。

要求：

- 1 <= s.length <= 3 * 105
- s 由整数和算符 '+', '-', '*', '/' 组成，中间由一些空格隔开
- s 表示一个 有效表达式
- 表达式中的所有整数都是非负整数，且在范围 [0, 231 - 1] 内
- 题目数据保证答案是一个 32-bit 整数

解题思路:

由于乘除优先于加减计算，因此，先进行所有乘除运算，并将这些乘除运算后的整数值放回原表达式的相应位置，则随后整个表达式的值，就等于一系列整数加减后的值。

基于此，我们可以用一个栈，保存这些（进行乘除运算后的）整数的值。对于加减号后的数字，将其直接压入栈中；对于乘除号后的数字，可以直接与栈顶元素计算，并替换栈顶元素为计算后的结果。

具体来说，遍历字符串 s，并用变量 last_sign 记录每个数字之前的运算符，对于第一个数字，其之前的运算符视为加号。每次遍历到数字末尾时，根据 last_sign 来决定计算方式：
- 加号：将数字压入栈
- 减号：将数字的相反数压入栈
- 乘除号：计算数字与栈顶元素，并将栈顶元素替换为计算结果

```python
class Solution:
    def calculate(self, s: str) -> int:
        stack = []

        cur_num = 0      # 当前数
        last_sign = '+'  # 上一个运算符

        s += '+' # 末尾补充一个+（是为了最后一个数字也能参与计算）
        
        for ch in s:
            if ch >= '0' and ch <= '9': # 数字
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


