
- [394] 字符串解码


# 1. 栈 <==>队列

:artificial_satellite: 栈、队列（相互实现）

没有什么技巧可言

- 添加时，都是向data中push元素
- 写法注意：因为C++的pop函数返回值为void，因此，要先top/front，所以，在书写代码pop的时候，要转换为两行（为了防止漏写，将下面2行写在一行）
  - int top = help.top();     help.pop();
  - int ret = data.front();   data.pop();


### 1.1. [232] 用栈实现队列

```c++
class MyQueue {
    stack<int> data;
    stack<int> help;
public:
    MyQueue() {

    }
    
    void push(int x) {
        data.push(x);
    }
    
    int pop() {
        if (empty()) return -1;
        if (!help.empty()) {
            int top = help.top(); help.pop();
            return top;
        }
        while (!data.empty()) {
            int top = data.top(); data.pop();
            help.push(top);
        }
        int top = help.top(); help.pop();
        return top;
    }
    
    int peek() {
        if (empty()) return -1;
        if (!help.empty()) {
            int top = help.top();
            return top;
        }
        while (!data.empty()) {
            int top = data.top(); data.pop();
            help.push(top);
        }
        int top = help.top();
        return top;
    }
    
    bool empty() {
        return data.empty() && help.empty();
    }
};
```

### 1.2. [225] 用队列实现栈

```c++
class MyStack {
    queue<int> data;
    queue<int> help;
public:
    MyStack() {

    }
    
    void push(int x) {
        data.push(x);
    }
    
    int pop() {
        if (this->empty()) {
            return -1;
        }
        while (data.size() > 1) {
            int f = data.front(); data.pop();
            help.push(f);
        }
        int ans = data.front(); data.pop();
        swap(data, help);
        return ans;
    }
    
    int top() {
        if (this->empty()) {
            return -1;
        }
        while (data.size() > 1) {
            int f = data.front(); data.pop();
            help.push(f);
        }
        int ans = data.front(); 
        return ans;
    }
    
    bool empty() {
        return data.empty() && help.empty();
    }
};

```

### 1.3. 😄[155] 最小栈

---

# 2. 栈: 基本题型


**python的list模拟栈**

- 获取栈顶：stack[-1]
- 弹出栈顶：stack.pop(-1)  或  stack.pop()

### 2.1. [71] 简化路径

```python
class Solution(object):
    def simplifyPath(self, path):
        stack = []
        for ch in path.split('/'):
            if ch == '.':   
                continue
            elif ch == '':  
                continue
            elif ch == '..': 
                if stack:
                    stack.pop(-1)
            else:
                stack.append(ch)
        return '/' + '/'.join(stack)
```

### 2.2. [20] 有效的括号

```python
class Solution(object):
    def isValid(self, s):
        stack = []
        hash = {
            "}": "{",
            ")": "(",
            "]": "[",
        }
        for ch in s:
            if ch in hash.values():
                stack.append(ch)
            else:
                if stack and hash[ch] == stack.pop(-1):
                    continue
                else:
                    return False
        return len(stack) == 0
```

### 2.3. [844] 比较含退格的字符串

```
输入：S = "ab#c", T = "ad#c"
输出：true
解释：S 和 T 都会变成 “ac”。
```

```python
class Solution(object):
  def backspaceCompare(self, s, t):
    def backspace(s):
      stack = []
      for ch in s:
        if ch == "#":
          if stack:
            stack.pop(-1)
        else:
          stack.append(ch)
      return ''.join(stack)
    return backspace(s) == backspace(t)
```



### 2.4. [1544] 整理字符串

```python
class Solution(object):
  def makeGood(self, s):
    stack = []
    for ch in s:
      if stack and abs(ord(stack[-1]) - ord(ch)) == abs(ord('a')-ord('A')):
        stack.pop(-1)
      else:
        stack.append(ch)
    return ''.join(stack)
```

### 2.5. [1047] 删除字符串中的所有相邻重复项


输入："abbaca"

输出："ca"

解释：在 "abbaca" 中，我们可以删除 "bb" 由于两字母相邻且相同，这是此时唯一可以执行删除操作的重复项。之后我们得到字符串 "aaca"，其中又只有 "aa" 可以执行重复项删除操作，所以最后的字符串为 "ca"。


```python
class Solution(object):
    def removeDuplicates(self, s):
        stack = []
        for ch in s:
            if stack and stack[-1] == ch:
                stack.pop(-1)
            else:
                stack.append(ch)
        return "".join(stack)
```

### 2.6. [1209] 删除字符串中的所有相邻重复项 II


输入：s = "deeedbbcccbdaa", k = 3

输出："aa"
解释： 
1. 先删除 "eee" 和 "ccc"，得到 "ddbbbdaa"
2. 再删除 "bbb"，得到 "dddaa"
3. 最后删除 "ddd"，得到 "aa"


- 解题关键: **栈中存放的是 [ch, ch出现次数]**

```python
class Solution(object):
  def removeDuplicates(self, s, k):
    stack = []  # [ch, 次数]
    for ch in s:
      if stack:
        s_ch, s_cnt = stack[-1]
        if s_ch == ch:
          if s_cnt == k - 1:
            stack.pop(-1)
          else:
            stack[-1][1] += 1
        else:
          stack.append([ch, 1])
      else:
        stack.append([ch, 1])

    ans = ""
    for ch, cnt in stack:
      ans += ch * cnt
    return ans
```

### 2.7. 😄 [394] 字符串解码

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: k[encoded_string]，表示其中方括号内部的 encoded_string 正好重复 k 次。注意 k 保证为正整数。

```c
示例 1：
输入：s = "3[a]2[bc]"
输出："aaabcbc"
示例 2：
输入：s = "3[a2[c]]"
输出："accaccacc"
```

```python
class Solution(object):
  def decodeString(self, s):
    stack = [] # [cur_str, num]
    cur_str = ""
    cur_num = 0
    for ch in s:
      if ch >= '0' and ch <= '9': # 数字
        cur_num = cur_num * 10 + int(ch)
      elif ch == '[': # 入栈
        stack.append([cur_num, cur_str])
        cur_num = 0
        cur_str = ""
      elif ch == ']': # 出栈
        num, str = stack.pop(-1)
        cur_num = 0
        cur_str = str + num * cur_str
      else: # 字母
        cur_str += ch
    return cur_str
```

--- 


# 3. 单调队列、单调栈

解题套路：先分析题意，是否要使用单调栈、单调队列，并知道数据的变化过程（代码的书写是有规律的，按照下面的写法，代码十分清晰）

```python
1. 从前向后，遍历每一个元素 (该元素一定会进入栈/队列)
	for r in range(size):
2. while循环, 保持单调性
	2.1. while 循环条件 and 题目条件
    		从[]中移除不满足单调性的栈顶/队尾，即:pop(-1)
	2.2. 当出 单调[] 后，可能需要(保存结果 + 更新条件)
3. ...
4. 元素进入 单调[]
	4.1. 当进入 单调[] 后，可能需要(更新结果 + 更新条件)
```


## 3.1. 单调栈

### 3.2.1. [739] 每日温度

```python
# 给定一个整数数组 temperatures ，表示每天的温度，返回一个数组 answer ，其中 answer[i] 是指对于第 i 天，下一个更高温度出现
# 在几天后。如果气温在这之后都不会升高，请在该位置用 0 来代替。 
```

- 栈中存放的元素，是下标，不是nums[i]

```python
class Solution(object):
    def dailyTemperatures(self, temperatures):
        size = len(temperatures) 
        ans = [0] * size # 保存结果[0, 0, 0, ... ...]
        stack = [] # 单调栈, 存放“下标”
        for i in range(size):
            while stack and temperatures[i] > temperatures[stack[-1]]: # 当前温度 > 栈顶温度
                idx = stack.pop(-1) # 出栈前，得到结果，保存到ans中
                ans[idx] = i-idx
            stack.append(i)
        return ans
```

### 3.2.2. [402] 移掉K位数字

给定一个以字符串表示的非负整数 *num*，移除这个数中的 *k* 位数字，使得剩下的数字最小。

```
输入: num = "1432219", k = 3
输出: "1219"
解释: 移除掉三个数字 4, 3, 和 2 形成一个新的最小的数字 1219	。
```

```python
class Solution(object):
    def removeKdigits(self, num, k):
        stack = []
        # 1.保持单调栈
        for ch in num:
            while stack and ch < stack[-1] and k > 0:  # 单调递增栈
                stack.pop(-1)
                k -= 1
            stack.append(ch)
        
        # 2. 别忘记(k>0)时，要处理下喔~
        while k > 0:
            stack.pop(-1)
            k -= 1

        ans = ''.join(stack).lstrip('0')
        return '0' if len(ans) == 0 else ans
```



### 3.2.3. 😄[496] 下一个更大元素 I

给定两个 `没有重复元素` 的数组 nums1 和 nums2 ，其中nums1 是 nums2 的子集。找到 nums1 中每个元素在 nums2 中的下一个比其大的值。

nums1 中数字 x 的下一个更大元素是指 x 在 nums2 中对应位置的右边的第一个比 x 大的元素。如果不存在，对应位置输出 -1 。

```
输入: nums1 = [4,1,2], nums2 = [1,3,4,2].
输出: [-1,3,-1]
解释:
    对于num1中的数字4，你无法在第二个数组中找到下一个更大的数字，因此输出 -1。
    对于num1中的数字1，第二个数组中数字1右边的下一个较大数字是 3。
    对于num1中的数字2，第二个数组中没有下一个更大的数字，因此输出 -1。
```

解题思路

1. 遍历nums2，保持单调栈，将不符合单调栈的情况记录到哈希表中（元素，第一个比元素大的值）
2. 遍历nums1，从哈希表中找到比他大的值

```python
class Solution(object):
    def nextGreaterElement(self, nums1, nums2):
        # 1. 遍历nums2，保持单调栈，将不符合单调栈的情况记录到哈希表中，hash=<元素，第一个比该元素大的元素>
        hash = {}  # <元素，第一个比该元素大的元素>
        stack = []
        for ch in nums2:
            while stack and ch > stack[-1]:
                small = stack.pop(-1)
                hash[small] = ch
            stack.append(ch)
        # 2. 遍历nums1，从哈希表中找到比他大的值
        size = len(nums1)
        ans = [-1] * size
        for i in range(size):
            if nums1[i] in hash:
                ans[i] = hash[nums1[i]]
        return ans
```



### 3.2.4. [503] 下一个更大元素 II 

给定一个循环数组（最后一个元素的下一个元素是数组的第一个元素），输出每个元素的下一个更大元素。数字 x 的下一个更大的元素是按数组遍历顺序，这个数字之后的第一个比它更大的数，这意味着你应该循环地搜索它的下一个更大的数。如果不存在，则输出 -1。

```shell
输入: [1,2,1]
输出: [2,-1,2]
解释: 第一个 1 的下一个更大的数是 2；
数字 2 找不到下一个更大的数； 
第二个 1 的下一个最大的数需要循环搜索，结果也是 2。
```


