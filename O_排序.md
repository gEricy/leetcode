
### 😭 [148] 链表排序


链表排序(归并)

```cpp
class Solution {
public:
    // 找中间节点(顺便将链表一分为二)  [head, pre], [slow, 末尾节点]
    ListNode* getMid(ListNode* head) {
        ListNode *pre; // 保存slow的前驱
        ListNode *slow = head;
        ListNode *fast = head;
        // 寻找中间节点: fast走2步，slow走1步，当fast走到末尾时，slow指向中间节点
        while (fast && fast->next) {
            pre = slow;
            slow = slow->next;
            fast = fast->next->next;
        }
        pre->next = NULL; // 切断链表 [head, pre], [slow, 末尾节点]
        return slow;
    }

    // 合并两个有序链表
    ListNode* merge(ListNode *L1, ListNode *L2) {
        if (!L1) return L2;
        if (!L2) return L1;

        ListNode dummy;
        ListNode *p = L1, *q = L2, *cur = &dummy;

        while (p && q) {
            ListNode *newNode = NULL;
            if (p->val < q->val) {
                newNode = p;
                p = p->next;
            } else {
                newNode = q;
                q = q->next;
            }
            cur->next = newNode;
            cur = cur->next;
        }
        if (p) cur->next = p;
        if (q) cur->next = q;

        return dummy.next;
    }

    ListNode* sortList(ListNode* head){
        if(!head || !head->next) // 归并排序递归条件: 至少2个节点
            return head;

        ListNode* mid = getMid(head);    // 将链表切成2半
        ListNode* L1 = sortList(head);   // 左递归排序
        ListNode* L2 = sortList(mid);    // 右递归排序
        return merge(L1, L2);            // 合并
    }
};
```



# 1. 快排

### 1.1. [912] 排序数组

- 注意: 采用快速排序竟然会超时

```python
class Solution(object):
  def partition(self, nums, l, r):
    left = l
    pivot = nums[l]
    while l<r: # 按照基准pivot，将数组分成2个部分
      # 先右后左
      while l<r and nums[r]>=pivot:
        r -= 1
      while l<r and nums[l]<=pivot:
        l += 1
      nums[l], nums[r] = nums[r], nums[l]
    # 新旧基准做交换
    nums[l], nums[left] = nums[left], nums[l]
    # 返回新的基准位置: l和r都可以
    return l

  def quickSort(self, nums, l, r):
    if l < r: # 至少有2个元素
      pivot = self.partition(nums, l, r)
      self.quickSort(nums, l, pivot-1)
      self.quickSort(nums, pivot+1, r)

  def sortArray(self, nums):
    l, r = 0, len(nums)-1
    self.quickSort(nums, l, r)
    return nums
```

### 1.2. [215] 数组中的第K个最大元素

- 快排的partition过程，每次可以确定一个元素的最终位置pivot
- 查看pivot与target的关系，分为下面3种情况
  - pivot == target: 找到
  - pivot > target: 目标索引target在[l, pivot-1]中 ==> r = pivot-1
  - pivot < target: 目标索引target在[pivot+1, r]中 ==> l = pivot+1

```python
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```

```python
class Solution(object):
    def partition(self, nums, l, r):
        pivot = nums[l]
        left = l

        while l < r:
            while l < r and pivot <= nums[r]:
                r -= 1
            while l < r and pivot >= nums[l]:
                l += 1
            nums[r], nums[l] = nums[l], nums[r]

        nums[left], nums[l] = nums[l], nums[left]

        return l

    def findKthLargest(self, nums, k):
        n = len(nums)

        k_index = n - k  # 从小到大排序，第K大元素 存储在 下标 len(n)-K

        l = 0
        r = n - 1
        while 1:
            pivot = self.partition(nums, l, r)
            if pivot == k_index:
                return nums[pivot]
            elif pivot < k_index:
                l = pivot + 1
            else:
                r = pivot - 1

        return -1
```



---

# 2. 堆排

### 2.1. [912] 排序数组

```c++
class Solution {
public:
    // root: 以root为根节点，向下调整
    // N: 数组总个数
    void down(vector<int>& A, int root, int N) {
        int i = 2*root+1;
        while (i < N) {
            if (i+1 < N && A[i] < A[i+1]) { // 存在右孩子 && 左孩子 < 右孩子
                i++; // i指向右孩子（右孩子更大）
            }
            if (A[root] > A[i]) { // 已经是大根堆了，不需要再调整了，直接break
                break;
            } else {
                swap(A[root], A[i]); // 将根和较大的孩子值交换
                root = i;     // 更新root，指向较大的孩子
                i = 2*root+1; // 更新左孩子，继续遍历
            }
        }
    }

    vector<int> sortArray(vector<int>& A) {
        // 创建大根堆: 以每个根节点作为调整的对象，从后向前调整
        int N = A.size();
        for (int i=N/2-1; i>=0; i--) {
            down(A, i, N);
        }

        // 堆顶元素是结果, 一个个取出来
        for (int i=N-1; i>=0; i--) {
            swap(A[0], A[i]); // 堆顶元素放在最后，确定一个元素的排序位置
            down(A, 0, i); // 数组长度: 一直缩短
        }
        
        return A;
    }
};
```


堆排序的应用: 优先级队列

### 2.2. [23] 合并 K 个升序链表

  > 给你一个链表数组，每个链表都已经按升序排列。
  >
  > 请你将所有链表合并到一个升序链表中，返回合并后的链表。

```c
定义：priority_queue<Type, Container, Functional>
Type        数据类型
Container   容器类型
Functional  比较方式
```

```c++
class Solution {
public:
    struct cmp{
        int operator()(ListNode* a, ListNode* b){ 
            return a->val > b->val;
        }
    };
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        ListNode dummy;
        ListNode* cur = &dummy;
        
        priority_queue<ListNode*, vector<ListNode*>, cmp> pq; // 创建小根堆

        // 将第一个元素加入pq堆
        for (auto elem : lists){ // elem是ListNode*，链表头节点
            if(elem){
                pq.push(elem);
            }
        }
        while (!pq.empty()){
            // 弹出堆顶(一定是最小值)，将节点的next再次插入堆中
            ListNode* top = pq.top(); pq.pop(); 
            if(top->next){
                pq.push(top->next);
            }
            // 堆顶top添加到结果链表
            cur->next = top; cur = cur->next;
        }
        
        return dummy.next;
    }
};
```



### 2.3. [295] 数据流的中位数

```c++
必须保证小根堆中的最小元素 >= 大根堆中的最大元素
（即：小根堆的堆顶 >= 大根堆的堆顶）

依次取出流中的数vec[i]，向大根堆/小根堆中放：
「原则」
① 大根堆的最大值(堆顶) ≤ 小根堆的最小值(堆顶)
② 大根堆元素总数 - 小根堆元素总数 不大于 1
③ 偶数次插入，总是插入到大根堆; 奇数次插入，总是插入到小根堆

注意：为了满足②③，要维持以上3个性质，「插入过程」需要稍微调整

1.先放入大根堆，再放入小根堆；再放入大根堆，再放入小根堆；... ...，直到全部放完
2.1. 将vec[i]放入大根堆之前，要拿小根堆的堆顶元素Top与vec[i]比较
        如果vec[i]<Top，则vec[i]直接放入大根堆
        如果vec[i]>Top，则将Top放入大根堆；将vec[i]放入小根堆
2.2. 将vec[i]放入大根堆，要拿大根堆的堆顶元素Top与vec[i]比较
        如果vec[i]>Top，则vec[i]直接放入大根堆
        如果vec[i]<Top，则将Top放入小根堆；将vec[i]放入大根堆
```

```c++
class Solution {
public:
    Solution() :size(0){}
public:
    void Insert(int num){
        if (size%2 == 0){   //size==偶数时，插入大根堆
            // 大根堆为空，直接插入
            if (size == 0){
                MaxPQ.emplace(num);
            }
            else{
                // 当前数 > 小根堆堆顶: 1.小根堆堆顶元素，弹出，插入到大根堆 2. 当前数，插入到小根堆
                int top = MinPQ.top();
                if (num > top){
                    MinPQ.pop(); MinPQ.push(num);
                    MaxPQ.push(top);
                }
                else // 否则，当前数，直接插入大根堆
                    MaxPQ.push(num);
            }
        }
        else{  //size==奇数时，插入小根堆
            int top = MaxPQ.top();
            // 当前数 < 大根堆堆顶: 1.大根堆堆顶元素，弹出，插入到小根堆 2.当前数，插入到大根堆
            if (num < top){
                MaxPQ.pop(); MaxPQ.push(num);
                MinPQ.push(top);
            }
            else  // 否则，当前数，直接插入小根堆
                MinPQ.push(num);
        }
        size++; //不要忘记，插入元素后，size++
    }

    double GetMedian(){
        if (size % 2 == 0)
            return (MaxPQ.top() + MinPQ.top())*1.0 / 2;
        else
            return MaxPQ.top();
    }
private:
    priority_queue<int> MaxPQ;
    priority_queue<int,vector<int>,greater<int>> MinPQ;
    int size;  // 数据流一共来了多少个数: 大根堆/小根堆总元素个数
};

int main(){
	Solution obj;
	obj.Insert(11);
	obj.Insert(3);
	obj.Insert(4);
	obj.Insert(20);
	obj.Insert(5);
	obj.Insert(7);
	obj.Insert(6);
	obj.Insert(8);
	double ret = obj.GetMedian();
}
```

### 2.4. N个`有序数组`整体最大的TopK

```c++
例如，输入含有N行元素的二维数组可以代表N个一维数组
219，405，538，845，971
148，558
52，99，348，691
再输入整数k=5，则打印：
Top5: 971，845，691，558，538

核心：PQ中存放的元素pair<int,int>--><元素值, 元素来自哪个数组>

1.  构建一个大小为N的大顶堆<元素值，元素来自哪个数组>
2.  取出并打印当前堆顶元素top，必然是最大值，获得top来自哪个数组top.second
3.1 如果vec[top.second].size()!=0--->将vec[top.second]的下一个元素继续入队
3.2 如果vec[top.second]==0,不操作
4.  执行步骤2~3，直到打印前K个。
```

```c++
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

vector<int> getTopK(vector<vector<int>> vec, int K){
    vector<int> ans;
    priority_queue<pair<int,int>> PQ;  // pair: <nums[i], 该数所属第几个数组>

    //用vec[i].back()，每个数组的最大的数，建立大根堆PQ
    for (int i = 0; i < vec.size(); i++) {
        // 将每个有序数组最后一个元素: 插入PQ & 弹出
        PQ.emplace(vec[i].back(), i); vec[i].pop_back(); 
    }

    while(!PQ.empty()) {
        pair<int, int> top = PQ.top(); PQ.pop(); //弹出PQ堆顶元素top
        ans.push_back(top.first);
        
        if (--K == 0) {
            break;
        }

        int index = top.second;
        if (!vec[index].empty()){ // 数组不为空
            PQ.emplace(vec[index].back(), index); // 数组最后一个元素插入PQ & 弹出
            vec[index].pop_back();
        }
    }

    return ans;
}

//求K个数组的前K大数
int main(){
	vector<vector<int>> vec = {
		{ 2, 5, 6, 6, 8, 10 },
		{ 1, 5, 6, 9 },
		{ 1, 4, 7, 7 }
	};
	vector<int> ret = getTopK(vec,5);
	for (auto node : ret)
		cout << node << endl;
}
```


---

# 3. 归并排序

### 3.1. [912] 排序数组

```c++
class Solution {
public:
    void merge(vector<int>& A, int l, int mid, int r) {
        // 1. 辅助数组B: 长度=R-L+1
        vector<int> B(r-l+1, 0);
        int k = 0;

        // 2. 两个有序数组A[l,mid] A[mid+1,r]合并成一个有序数组，将结果保存在辅助数组
        int i = l;
        int j = mid+1;
        while (i <= mid && j <= r) {
            B[k++] = A[i] < A[j] ? A[i++] : A[j++];
        }
        while (i <= mid) {
            B[k++] = A[i++];
        }
        while (j <= r) {
            B[k++] = A[j++];
        }

        // 3. 将辅助数组B中排好序的结果，copy到原数组A
        for (int i=l; i<=r; i++) {
            A[i] = B[i-l];
        }
    }

     // 归并排序
    void sort(vector<int>& A, int l, int r) {
        if (l < r) { // 至少2个元素，才能执行归并排序
            int mid = (l+r) / 2;
            sort(A, l, mid);
            sort(A, mid+1, r);
            merge(A, l, mid, r);
        }
    }

    vector<int> sortArray(vector<int>& A) {
        int N = A.size();
        sort(A, 0, N-1);
        return A;
    }
};
```


### 3.2. [剑指 Offer 51. 数组中的逆序对](https://leetcode.cn/problems/shu-zu-zhong-de-ni-xu-dui-lcof/)

LCR 170. 交易逆序对的总数

在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

```c++
class Solution {
    int ans = 0;
public:
    void merge(vector<int>& A, int l, int mid, int r) {
        
        // 1. 辅助数组
        vector<int> B(r-l+1, 0); 
        int k = 0;

        // 2. 两个有序数组合并，结果保存到辅助数组B中
        int i = l;
        int j = mid+1;
        while (i <= mid && j <= r) {
            if (A[i] > A[j]) { // 逆序对: A[i...mid], A[j]
                ans += mid-i+1; // 相比于归并排序[写法3]，就这里多了一行统计代码，其他一样
                B[k++] = A[j++];
            } else {
                B[k++] = A[i++];
            }
        }
        while (i <= mid) {
            B[k++] = A[i++];
        }
        while (j <= r) {
            B[k++] = A[j++];
        }
        
        // 3. 将辅助数组B中的数据，copy到A
        for (int i=l; i<=r; i++) {
            A[i] = B[i-l];
        }
    }
    void mergeSort(vector<int>& A, int l, int r) {
        if (l < r) {
            int mid = (l+r)/2;
            mergeSort(A, l, mid);
            mergeSort(A, mid+1, r);
            merge(A, l, mid, r);
        }
    }
    int reversePairs(vector<int>& A) {
        mergeSort(A, 0, A.size()-1);        
        return ans;
    }
};
```

### 3.3. 小和

```c++
class Solution {
    int ans = 0;
public:
    void merge(vector<int>& A, int l, int mid, int r) {
        
        // 1. 辅助数组
        vector<int> B(r-l+1, 0); 
        int k = 0;

        // 2. 两个有序数组合并，结果保存到辅助数组B中
        int i = l;
        int j = mid+1;
        while (i <= mid && j <= r) {
            if (A[i] < A[j]) { // 小和: A[i]和A[j...r]组成了小和对
                ans += A[i] * (r-j+1);  // 相比于归并排序[写法3]，就这里多了一行统计代码，其他一样
                B[k++] = A[i++];
            } else {
                B[k++] = A[j++];
            }
        }
        while (i <= mid) {
            B[k++] = A[i++];
        }
        while (j <= r) {
            B[k++] = A[j++];
        }
        
        // 3. 将辅助数组B中的数据，copy到A
        for (int i=l; i<=r; i++) {
            A[i] = B[i-l];
        }
    }
    void mergeSort(vector<int>& A, int l, int r) {
        if (l < r) {
            int mid = (l+r)/2;
            mergeSort(A, l, mid);
            mergeSort(A, mid+1, r);
            merge(A, l, mid, r);
        }
    }
    int reversePairs(vector<int>& A) {
        mergeSort(A, 0, A.size()-1);        
        return ans;
    }
};
```

