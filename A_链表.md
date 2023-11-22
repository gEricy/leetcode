代码编写注意事项：
1. 向前走N步、指向第N个节点：for(int i=0; ???; i++)

难题集合
- [25] K 个一组翻转链表
- [148] 排序链表
- [23] 合并K个升序链表（逻辑简单，使用到stl库不好记）


# 1.反转

### 1.1.[206] 反转链表

递归版本

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (!head || !head->next) { // 至少有2个元素才递归
            return head;
        }
        // 递归后半段
        ListNode* newHead = reverseList(head->next);
        // 处理前半段
        head->next->next = head;
        head->next = nullptr;
        return newHead;
    }
};
```

非递归版本

```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* pre = nullptr;
        ListNode* cur = head;
        while (cur) {
            // 保存后继
            ListNode* post = cur->next;
            // 逆序: 反指
            cur->next = pre;
            // 向后遍历
            pre = cur;
            cur = post;
        }
        return pre;
    }
};
```

### 1.2. 逆序打印链表

```cpp
class Solution {
public:
    vector<int> ret;
    vector<int> reversePrint(ListNode* head) {
        if (!head)  // 递归终止条件：链表空
            return {};

        // 将链表看作2部分: head, [head->next, 剩余]
        
        ListNode* node = head->next;  // 第2部分
        
        reversePrint(node);   	  // step1: 先打印第2部分
        ret.push_back(head->val); // step2: 再打印第1部分
        
        return ret;
    }
};
```

### 1.3. [24] 两两交换链表中的节点

```cpp
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (!head || !head->next) { // 递归条件: 至少有2个节点
            return head;
        }
        // 递归后半部分
        ListNode* postHead = swapPairs(head->next->next);
        // 处理前半部分
        ListNode* newHead = head->next;
        newHead->next = head;
        newHead->next->next = postHead;

        return newHead;
    }
};
```


### 1.4. [92] 反转从位置m到n的链表

要求：一趟扫描完成反转

```cpp
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        if (!head) return head;

        // 增加伪头结点
        ListNode dummy;
        dummy.next = head;

        ListNode* pre = &dummy;
        ListNode* cur = head;

        // 寻找翻转的点
        int i = 1;
        while (i < left) { // 循环退出时，cur指向第left个节点
            pre = pre->next;
            cur = cur->next;
            i++;
        }
        ListNode* pre1 = pre;
        ListNode* cur1 = cur;

        // 边遍历，边翻转: 翻转[left,right]区间内的节点
        while (i <= right) { // 循环退出时，cur指向第right个节点的后继
            ListNode* post = cur->next;
            cur->next = pre;
            pre = cur; cur = post;
            i++;
        }

        ListNode* pre2 = pre;
        ListNode* cur2 = cur;

        // 重新连接
        pre1->next = pre2;
        cur1->next = cur2;

        return dummy.next;
    }
};
```

### 1.5. 😭[25] K 个一组翻转链表

解法1: 递归

```cpp
class Solution {
public:
    void reverse(ListNode* head, ListNode* tail) {
        ListNode* pre = nullptr;
        ListNode* cur = head;

        while (cur != tail) { // 退出条件
            ListNode* post = cur->next;
            cur->next = pre;
            pre = cur; cur = post;
        } // 循环退出后，此时: cur指向tail; tail和tail前一个节点还未翻转
        cur->next = pre;
    }
    ListNode* reverseKGroup(ListNode* head, int k) {
        if (!head)
            return head;

        // 锁定翻转区间 [head, 第k个节点]
        ListNode* cur = head;
        for (int i=1; i<k; i++) {
            cur = cur->next;
            if (!cur) // 递归退出条件，不足k个节点，直接退出
                return head;
        }
        ListNode* part1Head = head;
        ListNode* part1Tail = cur;

        // 翻转后半段
        ListNode* part2 = cur->next;
        ListNode* part2Head = reverseKGroup(part2, k);
        // 翻转前半段
        reverse(part1Head, part1Tail);
        // 重新连接
        part1Head->next = part2Head;
        // 返回新的头结点
        return part1Tail;
    }
};
```

解法2: 非递归

```cpp
class Solution {
public:
    // 翻转[head, tail]区间内的链表, 返回新的{头, 尾}
    pair<ListNode*, ListNode*> myReverse(ListNode* head, ListNode* tail) {
        ListNode* pre = NULL;
        ListNode* cur = head;
        ListNode* end = tail->next; // 循环终止条件: cur == end
        while (cur != end) {
            // 保存post
            ListNode* post = cur->next;
            // 反指
            cur->next = pre;
            // 更新
            pre = cur;
            cur = post;
        }
        return {tail, head};
    }

    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode* dummy = new ListNode(0);
        dummy->next = head;

        ListNode* pre = dummy;  // 始终指向待翻转[head,tail]的前驱
        // head: 指向第一个有效节点
        
        while (head) {
            ListNode* tail = pre;
            // 1.查找待翻转的尾节点位置, 即:[head,tail]中的tail
            for (int i = 0; i < k; i++) {
                tail = tail->next;
                if (!tail) {
                    return dummy->next;
                }
            }
            // 2.先保存[head,tail]区间的下一个节点, 即tail->next
            ListNode* post = tail->next;

            // 3.将[head,tail]区间内的链表翻转
            pair<ListNode*, ListNode*> result = myReverse(head, tail);
            head = result.first;
            tail = result.second;

            // 4.“重新连接”: 将翻转后的子链表[head,tail], 重新连接到原链表
            pre->next = head;
            tail->next = post;

            // 5.更新pre,head: 即pre,head向前走, 用于翻转下k个元素
            pre = tail;
            head = tail->next;
        }

        return dummy->next;
    }
};
```


### 1.6. [234] 回文链表

> 1. 找中间节点（一定要通过fast，判断链表奇/偶）
> 2. 后半部分入栈
> 3. 出栈比较

```cpp
class Solution {
public:
    ListNode* getMidNode(ListNode* head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast && fast->next) {
            fast = fast->next->next;
            slow = slow->next;
        }
        return fast ? slow->next : slow;
    }

    bool isPalindrome(ListNode* head) {
        if (!head) {
            return true;
        }
        // 查询中间节点
        ListNode* midNode = getMidNode(head); 
        
        // 将后半部分放入栈
        stack<int> s;
        while (midNode) {
            s.push(midNode->val);
            midNode = midNode->next;
        }

        // cmp: 出栈元素、前半部分
        ListNode* cur = head;
        while (!s.empty() ) {
            if (s.top() != cur->val) {
                return false;
            }
            cur = cur->next;
            s.pop();
        }

        return true;
    }
};
```

# 2. 链表与【小技巧】

### 2.1. 😭[160] 相交链表

- 求两个相交链表的交点

解题思路：
1. 循环条件：p != q
2. p、q分别遍历L1+L2、L2+L1

```cpp
class Solution {
public:
    // 思想：L1+L2 == L2+L1
    // 举例: L1 = 1 -> 2 -> ×    L2 = 3 -> ×，无交点
    //     执行步骤: 
    //     L1+L2 = 1 -> 2 -> × -> 3 -> ×
    //     L2+L3 = 3 -> × -> 1 -> 2 -> ×
    //                            ↑（相等，退出循环）
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* p = headA;
        ListNode* q = headB;

        while (p != q) { // 循环退出条件: p和q走到同一个点(循环结束时，要么是交点，要么是null)
            p = p ? p->next : headB; // pA,pB走到链表尾部, 就切换到对方的链表头
            q = q ? q->next : headA;
        }

        return p;
    }
};
```


### 2.2. 😭[19] 删除链表的倒数第 N 个结点

解题思路
1. 快指针先走K步
2. 慢指针从头开始，快慢指针一起走，当快指针走到null时，慢指针指向的位置就是待删除节点


注意事项
1. 因为第一个节点可能会被删除，所以要增加伪头结点
2. 快慢指针开始的时候，指向第一个节点(head)


```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if (!head) return head;

        ListNode dummy;
        dummy.next = head;

        // 快指针: 向前走N步
        ListNode* fast = head;
        for (int i=1; i<=n; i++) {
            fast = fast->next;
        }

        // 快/慢指针一起向后走，当快指针指向null时，慢指针指向待删除节点(倒数第 N 个结点)
        ListNode* pre = &dummy;
        ListNode* slow = head;
        while (fast) {
            fast = fast->next;
            pre = slow;
            slow = slow->next;
        }
        // 删除: 慢指针指向待删除节点(倒数第 N 个结点)
        pre->next = slow->next;
        
        return dummy.next;
    }
};
```

### 2.3. 链表有环？环的入口节点

#### 2.3.1. [141] 环形链表

- 判断链表是否有环

```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        if (!head) {
            return false;
        }
        ListNode* fast = head;
        ListNode* slow = head;

        while (fast && fast->next) {
            fast = fast->next->next;
            slow = slow->next;
            if (fast == slow) {
                return true;
            }
        }

        return false;
    }
};
```

#### 2.3.2. [142] 环形链表 II

- 返回环形链表的第一个交点

1. 快指针走2步，慢指针走1步
2. 有环
3. 环入口: 快指针移动到head，快慢指针一起走，第一次相遇节点就是入口

```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;

        bool found = false;
        while (fast && fast->next) { // 快指针走2步，慢指针走1步
            fast = fast->next->next;
            slow = slow->next;
            if (slow == fast) {
                found = true;
                break;
            }
        }
        if (!found) { // 有环
            return nullptr;
        }
        // 环入口: 快指针移动到head，快慢指针一起走，第一次相遇节点就是入口
        fast = head;
        while (fast != slow) {
            fast = fast->next;
            slow = slow->next;
        }
        return slow;
    }
};
```

### 2.4. [237] 删除链表中的节点

技巧：将node节点的值，改为node->next->val，删除node->next

```cpp
class Solution {
public:
    void deleteNode(ListNode* node) {
        node->val = node->next->val;
        node->next = node->next->next;
    }
};
```

### 2.5. [2] 两数相加


```cpp
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode dummy;
        ListNode* cur = &dummy;

        ListNode* p = l1;
        ListNode* q = l2;

        int carry = 0;
        while (p || q) { // 只要有一个链表不为空，就一直向后迭代
            int pval = p ? p->val : 0;
            int qval = q ? q->val : 0;
            if(p) p = p->next;
            if(q) q = q->next;

            int sum = pval + qval + carry;

            cur->next = new ListNode(sum%10);
            cur = cur->next;

            carry = sum/10;
        }

        if(carry > 0) {
            cur->next = new ListNode(carry);
        }

        return dummy.next;
    }
};
```

### 2.6. 😭[138] 复制带随机指针的链表

说明: 遍历3次

1. 拷贝节点
2. 给节点的random指针赋值
3. 拆分


```cpp
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if (!head)
            return head;

        // 1. 遍历一次，复制新节点 (此时，random指针未赋值)
        Node* cur = head;
        while (cur) {
            // 拷贝: 新建一个节点，插入之
            Node *newNode = new Node(cur->val, cur->next, NULL);
            cur->next = newNode;
            // cur向后移动
            cur = cur->next->next;
        }

        // 2. 遍历一次，给random赋值
        cur = head;
        while (cur) {
            cur->next->random = cur->random ? cur->random->next : NULL;
            cur = cur->next->next;
        }

        // 3. 遍历一次，拆分成2个链表
        Node l1(0); Node* cur1 = &l1;
        Node l2(0); Node* cur2 = &l2;

        cur = head;
        while (cur) {
            cur1->next = cur; cur1 = cur1->next;
            cur2->next = cur->next; cur2 = cur2->next;

            cur = cur->next->next;
        }
        cur1->next = nullptr; // 一定要设置为空
        cur2->next = nullptr; // 一定要设置为空

        return l2.next;
    }
};
```

---

# 3. 链表与【排序/有序】

### 3.1. 😭 [83] 删除排序链表中的重复元素 easy

给定一个已排序的链表的头 head ， 删除所有重复的元素，使每个元素只出现一次 。返回 已排序的链表 。

```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head || !head->next) return head;

        ListNode* cur = head;
        while (cur) {
            if (cur->next && cur->val == cur->next->val) {
                cur->next = cur->next->next; // 删除节点
            } else {
                cur = cur->next;
            }
        }

        return head;
    }
};
```

### 3.2. 😭 [82] 删除排序链表中的重复元素 II  --- mid

给定一个已排序的链表的头 head ， 删除原始链表中所有重复数字的节点，只留下不同的数字 。返回 已排序的链表 。

```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head || !head) return head;

        // 增加伪头结点
        ListNode dummy(-1);
        dummy.next = head;

        ListNode* pre = &dummy;
        ListNode* cur = head;

        while (cur) {
            // 寻找下一个节点
            bool repeat = false;
            while (cur->next && cur->val == cur->next->val) {
                repeat = true;
                cur = cur->next;
            }
            if (repeat) { // 存在重复的节点
                cur = cur->next;
                pre->next = cur;
            } else {
                pre = pre->next;
                cur = cur->next;
            }
        }

        return dummy.next;
    }
};
```


### 3.3. [21] 合并两个有序链表

```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode dummy;
        ListNode* cur = &dummy;

        ListNode* l1 = list1;
        ListNode* l2 = list2;

        while (l1 && l2) {
            if (l1->val < l2->val) {
                cur->next = l1;
                l1 = l1->next;
            } else {
                cur->next = l2;
                l2 = l2->next;
            }
            cur = cur->next;
        }
        while (l1) {
            cur->next = l1; l1 = l1->next;
            cur = cur->next;
        }
        while (l2) {
            cur->next = l2; l2 = l2->next;
            cur = cur->next;
        }
        return dummy.next;
    }
};
```

### 3.4. [86] 分隔链表

给你一个链表的头节点 head 和一个特定值 x ，请你对链表进行分隔，使得所有 小于 x 的节点都出现在 大于或等于 x 的节点之前。

你应当 保留 两个分区中每个节点的初始相对位置。

1. 将小于x的值，挂在small链表
2. 将大于x的值，挂在big链表
3. 链表连接：small + big

```cpp
class Solution {
public:
    ListNode* partition(ListNode* head, int x) {
        ListNode list1; ListNode* l1 = &list1;
        ListNode list2; ListNode* l2 = &list2;

        ListNode* cur = head;

        while (cur) {
            if (cur->val < x) {
                l1->next = cur;
                l1 = l1->next;
            } else {
                l2->next = cur;
                l2 = l2->next;
            }
            cur = cur->next;
        }

        l1->next = NULL; // 一定要设置为空
        l2->next = NULL; // 一定要设置为空

        // 连接: list1的最后一个节点，指向list2的第一个节点
        l1->next = list2.next;

        return list1.next;
    }
};
```

### 3.5. [328] 奇偶链表

给定单链表的头节点 head ，将所有索引为奇数的节点和索引为偶数的节点分别组合在一起，然后返回重新排序的列表。


```cpp
class Solution {
public:
    ListNode* oddEvenList(ListNode* head) {
        ListNode list1; ListNode* l1 = &list1;
        ListNode list2; ListNode* l2 = &list2;

        ListNode* cur = head;
        int idx = 1;
        while (cur) {
            if (idx % 2 == 1) {
                l1->next = cur;
                l1 = l1->next;
            } else {
                l2->next = cur;
                l2 = l2->next;
            }
            cur = cur->next;
            idx++;
        }

        l1->next = NULL; // 一定要设置为空
        l2->next = NULL; // 一定要设置为空

        // 连接: list1的最后一个节点，指向list2的第一个节点
        l1->next = list2.next;

        return list1.next;
    }
};
```



### 3.6. [23] 合并K个升序链表

> 给你一个链表数组，每个链表都已经按升序排列。
>
> 请你将所有链表合并到一个升序链表中，返回合并后的链表。

```c
定义：priority_queue<Type, Container, Functional>
Type        数据类型
Container   容器类型
Functional  比较方式
```

```c
class Solution {
public:
    struct cmp{
        int operator()(ListNode* a, ListNode* b){ // 仿函数对象
            return a->val > b->val;
        }
    };
    
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        ListNode dummy;
        ListNode* cur = &dummy;
        
        priority_queue<ListNode*, vector<ListNode*>, cmp> pq; // 创建小根堆
        
        // 初始化堆: 每个有序链表的链表头, 加入小根堆
        for (auto elem : lists){ // elem是ListNode*，链表头节点
            if(elem){
                pq.push(elem);
            }
        }

        while (!pq.empty()){
            ListNode* top = pq.top(); pq.pop(); // 弹出堆顶(一定是最小值)
            if(top->next){  // 将节点的next再次插入堆中
                pq.push(top->next);
            }

            // 堆顶top添加到结果链表
            cur->next = top;
            cur = cur->next;
        }
        return dummy.next;
    }
};
```


### 3.7. 😄 [148] 排序链表

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
