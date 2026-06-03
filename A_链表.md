代码编写注意事项：
1. 向前走N步、指向第N个节点：for(int i=0; ???; i++)

难题集合
- 😭[25] K 个一组翻转链表
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
        ListNode* part2Head = swapPairs(head->next->next);
        // 处理前半部分
        ListNode* newHead = head->next; // 新头结点
        newHead->next = head;     // 反指
        head->next = part2Head;   // 连接 part1 和 part2
        return newHead;           // 返回新头结点
    }
};
```


### 1.4. 😭[92] 反转从位置m到n的链表

要求：一趟扫描完成反转

```cpp
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        if (!head || left == right) return head;

        // 增加伪头结点
        ListNode dummy;
        dummy.next = head;

        ListNode* pre = &dummy;
        ListNode* cur = dummy.next;

        // 寻找翻转的点
        int i = 1;
        for (; i < left; i++) { // 寻找最左面的节点，假设left = 1，其实已经找到了
            pre = cur;
            cur = cur->next;
        }
        ListNode* pre1 = pre;
        ListNode* cur1 = cur;

        // 边遍历，边翻转: 翻转[left,right]区间内的节点
        for (; i <= right; i++) { // 循环退出时，cur指向第right个节点的后继
            ListNode* post = cur->next;
            cur->next = pre;
            pre = cur; cur = post;
        }

        // 重新连接新链表
        pre1->next = pre;
        cur1->next = cur;

        return dummy.next;
    }
};
```

### 1.5. 😭[25] K 个一组翻转链表

递归

```cpp
class Solution {
public:
    // 反转[left,right]之间的链表
    void reverseKGroup(ListNode* left, ListNode* right) {
        if (left == right) return;
        
        ListNode* pre = nullptr;
        ListNode* cur = left;
        while (cur != right) {
            ListNode* post = cur->next;
            cur->next = pre;
            pre = cur; cur = post;
        } // 循环退出后，此时: cur指向right; right和right前一个节点还未翻转
        
        cur->next = pre;
    }
    
    ListNode* reverseKGroup(ListNode* head, int k) {
        if (!head) {
            return head;
        }

        // 锁定翻转区间[left,right]
        ListNode* left = head;
        ListNode* right = head;
        for (int i=1; i<k; i++) { // 遍历结束时，[left,right]就是待反转的区间了
            right = right->next;
            if (!right) {
                return head;
            }
        }

        // 反转后半部分
        ListNode* part2 = reverseKGroup(right->next, k);
        // 反转前半段
        reverseKGroup(left, right);
        // 重连
        left->next = part2;
        // 返回新的头结点
        return right;
    }
};
```


### 1.6. [234] 回文链表

> 1. 找中间节点的后继（一定要通过fast，判断链表奇/偶）
> 2. 后半部分入栈
> 3. 出栈比较

```cpp
class Solution {
public:
    // 获取中间节点的后继
    ListNode* getMidNodePost(ListNode* head) {
        ListNode* s = head;
        ListNode* f = head;
        while (f && f->next) {
            s = s->next;
            f = f->next->next;
        }
        return f ? s->next : s;
    }
    bool isPalindrome(ListNode* head) {
        if (!head || !head->next) {
            return true;
        }
        // 1.找中间节点的后继
        ListNode* midNodePost = getMidNodePost(head);

        // 2.将后半段全部入栈
        stack<ListNode*> S;
        while (midNodePost) {
            S.push(midNodePost);
            midNodePost = midNodePost->next;
        }

        // 3.后半段出栈，和前半段对比val
        ListNode* cur = head;
        while (!S.empty()) {
            ListNode* top = S.top(); S.pop();
            if (cur->val != top->val) {
                return false;
            }
            cur = cur->next;
        }

        return true;
    }
};
```

# 2. 链表与【小技巧】

### 2.1. [160] 相交链表

- 求两个相交链表的交点

解题思路：
- p遍历 L1、L2
- q遍历 L2、L1

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* pA = headA;
        ListNode* pB = headB;

        while (pA || pB) { // 只要任何一个没结束，就向后遍历
            if (pA == pB) { // 相交节点
                return pA;
            }
            pA = pA ? pA->next : headB; // pA如果走到头，就去遍历headB
            pB = pB ? pB->next : headA; // pB如果走到头，就去遍历headA
        }

        return NULL;
    }
};
```


### 2.2. [19] 删除链表的倒数第 N 个结点

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
        ListNode dummy;
        dummy.next = head;

        // 快指针: 先走n步骤
        ListNode* fast = head;
        while (fast && n) {
            fast = fast->next;
            n--;
        }

        ListNode* pre = &dummy;

        ListNode* slow = head; // 快慢指针一起走，当fast走到NULL时，slow指向“被删除节点”
        while(fast) {
            fast = fast->next;
            pre = slow;
            slow = slow->next;
        }

        pre->next = slow->next; // 删除

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
        if (!head) return head;

        // 快慢指针一起走
        bool isCycle = false;
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast && fast->next) { // 循环退出条件: 有环，走到相交的点、无环，fast走到null
            fast = fast->next->next;
            slow = slow->next;
            if (slow == fast) {
                isCycle = true;
                break;
            }
        }
        // 无环
        if (!isCycle) {
            return NULL;
        }
        // 有环，慢指针重新指向head，快慢指针一起走，再次相遇的点就是第一个入口点
        slow = head;
        while (slow != fast) {
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
        if (!head) return head;

        Node* cur = head;
        
        // 遍历第1次，复制节点
        while (cur) {
            // 拷贝: 复制一个节点
            Node *newNode = new Node(cur->val, cur->next, NULL);
            // 插入
            cur->next = newNode;
            // cur向后移动
            cur = cur->next->next;
        }

        // 遍历第二次，顺便给random指针赋值
        cur = head;
        while (cur) {
            cur->next->random = cur->random ? cur->random->next : NULL;
            cur = cur->next->next;
        }

        // 遍历第三次，拆分链表
        Node head1(0,NULL,NULL); Node* cur1 = &head1; // 旧链表
        Node head2(0,NULL,NULL); Node* cur2 = &head2; // 新链表

        cur = head;
        while (cur) {
            // 新链表插入新节点
            cur2->next = cur->next; cur2 = cur2->next;
            // 旧链表插入旧节点
            cur1->next = cur; cur1 = cur1->next;
            // 向后遍历
            cur = cur->next->next;
        }

        cur1->next = nullptr; // 一定要设置为空
        cur2->next = nullptr; // 一定要设置为空

        return head2.next;
    }
};
```

---

# 3. 链表与【排序/有序】

### 3.1. [83] 删除排序链表中的重复元素 easy

给定一个已排序的链表的头 head ， 删除所有重复的元素，使每个元素只出现一次 。返回 已排序的链表 。

```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head) return head;

        ListNode dummy; dummy.next = head;

        ListNode* pre = &dummy;
        ListNode* cur = head;

        while (cur) {
            if (cur->next && cur->next->val == cur->val) {
                cur->next = cur->next->next; // 删除节点
            } else {
                cur = cur->next;
            }
        }

        return dummy.next;
    }
};
```

### 3.2. [82] 删除排序链表中的重复元素 II  --- mid

给定一个已排序的链表的头 head ， 删除原始链表中所有重复数字的节点，只留下不同的数字 。返回 已排序的链表 。

```cpp
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head) return head;

        ListNode dummy; dummy.next = head;

        ListNode* pre = &dummy;
        ListNode* cur = head;

        while (cur) {
            bool hasEqual = false;
            ListNode* ptr = cur->next;
            while (ptr && ptr->val == cur->val) {
                hasEqual = true;
                ptr = ptr->next;
            }
            if (hasEqual) {
                pre->next = ptr; // 删除
                cur = ptr;
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

        ListNode* p = list1;
        ListNode* q = list2;

        while (p && q) {
            int val;
            if (p->val <= q->val) {
                val = p->val;
                p = p->next;
            } else {
                val = q->val;
                q = q->next;
            }
            cur->next = new ListNode(val);
            cur = cur->next;
        }

        if (p) cur->next = p;
        if (q) cur->next = q;

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
    
        // 创建2个链表，将 < x 和 >= x 的值，分别挂在两个链表上
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

        priority_queue<ListNode*, vector<ListNode*>, cmp> Q; // 创建小根堆

        // 初始化堆: 每个有序链表的链表头, 加入小根堆
        for (auto elem : lists) {
            if (elem) {
                Q.push(list);
            }
        }

        while (!Q.empty()) {
            ListNode* node = Q.top(); Q.pop(); // 弹出堆顶(一定是最小值)
            cur->next = node; cur = cur->next; // 插入结果集合
            if (node->next) { // 将节点的next插入堆
                Q.push(node->next);
            }
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
