

# 1.反转

### 1.1.链表反转

- 链表逆序(递归版本) https://zhuanlan.zhihu.com/p/86745433

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (!head || !head->next) // 递归条件: 最少有2个节点
            return head;
        // 递归后半段
        ListNode* newHead = reverseList(head->next);
        // 处理前半段
        head->next->next = head;
        head->next = nullptr;

        return newHead;
    }
};
```

- 非递归版本

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* pre = NULL; // 不需要伪头节点, pre设为NULL
        ListNode* cur = head;
        while(cur){
            ListNode* post = cur->next; // 保存后继
            cur->next = pre;            // 逆序: 反指
            pre = cur; cur = post;      // pre,cur向后移动
        }
        return pre;  // 返回的是pre，不是head
    }
};
```

### 1.2. 逆序打印链表

```c++
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

### 1.3. 两两交换链表中的节点

```c++
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (!head || !head->next) // 递归条件：至少有2个节点
            return head;
        
        // 递归后半段
        ListNode* part2 = swapPairs(head->next->next); 
        // 处理前半段
        ListNode* newHead = head->next;
        newHead->next = head;
        head->next = part2;

        return newHead;
    }
};
```


### 1.4. [反转从位置m到n的链表](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

要求：一趟扫描完成反转

```c++
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        ListNode* pre = nullptr;
        ListNode* cur = head;
        
        // 查询开始翻转的点
        int i = 1;
        for (; i < left; i++) { // 循环退出后，cur指向第left个节点
            pre = cur;
            cur = cur->next;
        }

        ListNode* sPre = pre;
        ListNode* sCur = cur;

        // 边遍历，边翻转: 翻转[left,right]区间内的节点，所以，循环退出条件: i<(right+1)
        for (; i < right+1; i++) {  // 循环退出后，cur指向第right+1个节点
            ListNode* post = cur->next;
            cur->next = pre;
            pre = cur; cur = post;
        }

        // 连接
        if (sPre){
            sPre->next = pre;
        } else { // 当left=1时，特殊处理，此时head是pre
            head = pre;
        }
        sCur->next = cur;

        return head;
    }
};
```

### 1.5. [K个一组反转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

解法1: 递归

```c++
class Solution {
public:
    // 将[head, tail]翻转，返回新的头结点
    ListNode* reverse(ListNode* head, ListNode* tail) {
        ListNode* pre = nullptr;
        ListNode* cur = head;

        while (cur != tail) { // 退出条件
            ListNode* post = cur->next;
            cur->next = pre;
            pre = cur; cur = post;
        } // 循环退出后，此时: cur指向tail; tail和tail前一个节点还未翻转
        cur->next = pre;
        return cur;
    }

    ListNode* reverseKGroup(ListNode* head, int k) {
        if (!head)
            return head;

        // 锁定翻转区间：[head, 第k个节点]
        ListNode* cur = head;
        for (int i=1; i<k; i++) { // 注意: i=1
            cur = cur->next;
            if (!cur) // 递归退出条件
                return head;
        }

        // 翻转后半段
        ListNode* part2Head = reverseKGroup(cur->next, k);
        // 处理前半段
        ListNode* newHead = reverse(head, cur);
        head->next = part2Head;

        return newHead;
    }
};
```

解法2: 非递归

```c++
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


### 1.6. [回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/) **

> 1. 找中间节点（一定要通过fast，判断链表奇/偶）
> 2. 后半部分入栈
> 3. 出栈比较

```c++

class Solution {
public:
    ListNode* getlistmid(ListNode* head){
        ListNode* fast = head;
        ListNode* slow = head;
        
        while(fast && fast->next){
            fast = fast->next->next;
            slow = slow->next;
        }
        
        // 判断奇数偶数的技巧：使用fast
        //        fast不为NULL ==> 奇数 ==> 返回mid->next
        //        fast  为NULL ==> 偶数 ==> 返回mid
        return (fast) ? slow->next : slow;
    }

    // 核心难点：奇数偶数要区分开来！
    bool isPalindrome(ListNode* head) {
        if(!head)
            return true;

        stack<int> s;

        ListNode* mid = getlistmid(head);
        ListNode* cur = mid;

        // 将后半段(值), 入栈
        while(cur){
            s.push(cur->val);
            cur = cur->next;
        }

        // 栈中的元素(后半部分)与前半部分对比
        cur = head;
        while(cur != mid && !s.empty()){
            if(s.top() != cur->val)
                return false; 
            s.pop();
            cur = cur->next;
        }
        return true;
    }
};
```

# 2. 链表与【小技巧】

### 2.1. 相交链表

- 求两个相交链表的交点

解题思路：
1. 循环条件：p != q
2. p、q分别遍历L1+L2、L2+L1

```c++
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

        while (p != q) { // 循环退出条件: p和q走到同一个点
            p = p ? p->next : headB; // pA,pB走到链表尾部, 就切换到对方的链表头
            q = q ? q->next : headA;
        }

        return p;
    }
};
```


### 2.2. [删除链表的倒数第K个节点](https://leetcode-cn.com/problems/SLwz0R/submissions/)


解题思路
1. 快指针先走K步
2. 慢指针从头开始，快慢指针一起走，当快指针走到null时，慢指针指向的位置就是待删除节点


注意事项：因为第一个节点可能会被删除，所以要增加伪头结点


```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if (!head)
            return head;
        
        // 伪头结点: 因为第一个元素可能会被删除
        ListNode dummy; 
        dummy.next = head;

        // fast指向第N+1个节点（快指针向前走N步）
        ListNode* fast = head;
        for (int i=1; i<n+1; i++){ 
            fast = fast->next;
        }

        ListNode* pre = &dummy;
        ListNode* cur = dummy.next;

        // 快指针和慢指针一起走，当快指针为null时，慢指针指向被删节点
        while (fast) {
            fast = fast->next;
            pre = cur; cur = cur->next;
        }

        pre->next = cur->next;

        return dummy.next;
    }
};
```

### 2.3. 链表有环？环的入口节点

#### 2.3.1.环形链表①

- 判断链表是否有环

```c++
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;

        while (fast && fast->next) {
            fast = fast->next->next;
            slow = slow->next;
            if (slow == fast) {
                return true;
            }
        }
        
        return false;
    }
};
```

#### 2.3.2.环形链表②

- 返回环形链表的第一个交点

1. 快指针走2步，慢指针走1步
2. 有环: fast && fast->next
3. 环入口: 快指针移动到head，快慢指针一起走，第一次相遇节点就是入口

```c++
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;

        bool hasCycle = false;
        while (fast && fast->next) {
            fast = fast->next->next;
            slow = slow->next;
            if (fast == slow) {
                hasCycle = true;
                break;
            }
        }

        if (!hasCycle) {
            return NULL;
        }

        fast = head;

        while (slow != fast) {
            fast = fast->next;
            slow = slow->next;
        }
        
        return slow;
    }
};
```

### 2.4. 旋转链表

解题思路：
1. 先将链表首位相连，并统计链表总结点数cnt
2. 找规律：
    1. 规律：链表总结点数cnt = 向右旋转k次 + 最后一个节点的下标
    2. 结论：最后一个节点的下标 = 链表总结点数cnt - 向右旋转k次 = cnt - (k % cnt)
3. 处理
    1. 找到最后一个节点：找到第 cnt - (k % cnt) 个节点，断开
    2. 返回新的头结点：最后一个节点的后继

```c++
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        
        if (!head)
            return head;

        int cnt = 0;
        ListNode* cur = head;
        ListNode* pre = nullptr;
        while (cur) {
            pre = cur;
            cur = cur->next;
            cnt++;
        }
        pre->next = head; // 首尾相连

        int lastNodeIndex = cnt - (k % cnt); // 最后一个节点的下标 + 旋转k次数 = 总节点数cnt

        cur = head;
        for (int i=1; i<lastNodeIndex; i++) { // 找到最后一个节点
            cur = cur->next;
        }

        ListNode* newHead = cur->next;
        cur->next = nullptr; // 断开

        return newHead;
    }
};
```

### 2.5. [237] 删除链表中的节点

技巧：将node节点的值，改为node->next->val，删除node->next

```c++
class Solution {
public:
    void deleteNode(ListNode* node) {
        node->val = node->next->val;   
        node->next = node->next->next; 
    }
};
```

### 2.6. [2]两数相加


```c++
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        if (!l1)
            return l2;
        if (!l2) 
            return l1;

        ListNode* p = l1;
        ListNode* q = l2;


        ListNode dummy;
        ListNode* cur = &dummy;

        int carry = 0;
        while (p || q) {
            int pval=0, qval=0;
            if (p) {
                pval = p->val;
                p = p->next;
            }
            if (q) {
                qval = q->val;
                q = q->next;
            }

            int sum = pval + qval + carry;
            int val = sum % 10;
            carry = sum / 10;

            cur->next = new ListNode(val);

            cur = cur->next;
        }

        if (carry > 0) {
            cur->next = new ListNode(carry);
        }

        return dummy.next;
    }
};
```

### 2.6. 复制带随机指针的链表

1. 先拷贝
2. 再给random指针赋值
3. 拆分

```c++
class Solution {
public:
    Node* copyRandomList(Node* head) {

        if (!head) 
            return head;

        // 1.拷贝
        Node* cur = head;
        while (cur) {
            // 拷贝: 新建一个节点，插入之
            Node* nodeNew = new Node(cur->val);
            nodeNew->next = cur->next;
            cur->next = nodeNew;
            // cur向后移动
            cur = cur->next->next;
        }
        
        // 2.给random指针赋值
        cur = head;
        while (cur) {
            Node* tCur = cur->next;
            tCur->random = cur->random ? cur->random->next : nullptr;
            cur = cur->next->next;
        }

        // 3.拆分新的链表
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

### 3.1. 删除排序链表中重复的元素

题1： 83 easy

- 删除排序链表中的重复元素，相同元素只出现一次

```c++
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (!head || !head->next)
            return head;

        ListNode* pre = head;
        ListNode* cur = head->next;

        while(cur) {
            ListNode* post = cur->next;
            if (pre->val == cur->val) { // 元素重复
                pre->next = post; // 删除当前元素cur, pre不用更新
            } else { // 元素不重复
                pre = cur; // pre后移
            }
            cur = post;
        }

        return head;
    }
};
```

题2： 82  mid 😄

- 给定一个已排序的链表的头 head ， 删除原始链表中所有重复数字的节点，只留下不同的数字 。返回 已排序的链表 。

### 3.1. [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        ListNode* p = list1;
        ListNode* q = list2;

        ListNode dummy; // 伪头结点
        ListNode* cur = &dummy;

        while (p && q) {
            ListNode* node = nullptr;
            
            if (p->val <= q->val) {
                node = p;
                p = p->next;
            } else {
                node = q;
                q = q->next;
            }
            cur->next = node;
            cur = cur->next;
        }

        if (p) {
            cur->next = p;
        }
        if (q) {
            cur->next = q;
        }

        return dummy.next;
    }
};
```

### 3.2. [86] 分隔链表

给你一个链表的头节点 head 和一个特定值 x ，请你对链表进行分隔，使得所有 小于 x 的节点都出现在 大于或等于 x 的节点之前。

你应当 保留 两个分区中每个节点的初始相对位置。

1. 将小于x的值，挂在small链表
2. 将大于x的值，挂在big链表
3. 链表连接：small + big

```c++
class Solution {
public:
    ListNode* partition(ListNode* head, int x) {

        if (!head) {
            return head;
        }

        ListNode list1;
        ListNode list2;

        ListNode* cur1 = &list1;
        ListNode* cur2 = &list2;

        ListNode* cur = head;
        while (cur) {
            if (cur->val < x) {
                cur1->next = cur;
                cur1 = cur1->next;
            } else {
                cur2->next = cur;
                cur2 = cur2->next;
            }
            cur = cur->next;
        }
        cur1->next = nullptr; // 一定要设置为空
        cur2->next = nullptr; // 一定要设置为空

        // 连接
        cur1->next = list2.next;

        return list1.next;
    }
};
```

### 3.2. 奇偶链表

```c++
class Solution {
public:
    ListNode* oddEvenList(ListNode* head) {
        ListNode list1;
        ListNode list2;

        ListNode* cur1 = &list1;
        ListNode* cur2 = &list2;

        ListNode* cur = head;

        int i = 1;
        while (cur) {
            if (i % 2 != 0) {
                cur1->next = cur;
                cur1 = cur1->next;
            } else {
                cur2->next = cur;
                cur2 = cur2->next;
            }
            i++;
            cur = cur->next;
        }
        cur1->next = nullptr; // 一定要设置为空
        cur2->next = nullptr; // 一定要设置为空

        // 连接
        cur1->next = list2.next;

        return list1.next;
    }
};
```

### 3.2. 奇数位升序，偶数位降序链表排序

一个链表，奇数位升序偶数位降序，让链表变成升序的。

比如：<u>1</u> 8 <u>3</u> 6 <u>5</u> 4 <u>7</u> 2 <u>9</u>，最后输出1 2 3 4 5 6 7 8 9。

> 1. 新建两个链表，分别挂奇数和偶数
> 2. 奇数链表尾插； 偶数链表头插
> 3. 将两个有序链表 **[合并]**



### 3.3. [23. 合并K个`升序链表`😄](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

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
        
        // 每个有序链表的链表头, 加入小根堆
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


### 3.4. [148. 排序链表😄](https://leetcode-cn.com/problems/sort-list/)

1-插入排序 （会超时）

```c++
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        if (!head)
            return NULL;

        ListNode* dummy = new ListNode();

        while(head) {
            // 从头到尾，寻找当前元素head的插入位置
            ListNode* tpre = dummy;
            ListNode* tcur = dummy->next;
            while (tcur && tcur->val < head->val) {
                tpre = tcur;
                tcur = tcur->next;
            }

            ListNode* post = head->next;

            tpre->next = head;
            head->next = tcur;

            head = post;
        }
        return dummy->next;
    }
};
```

2-链表排序(归并)

```c++
class Solution {
public:
    // 找中间节点(顺便将链表一分为二)  [head, mid], [mid->next, 末尾节点]
    ListNode* getMid(ListNode* head) {
        ListNode *slow = head, *fast = head->next;
        while (!fast && !fast->next) {
            slow = slow->next;
            fast = fast->next->next;
        }
        ListNode* mid = slow->next;
        slow->next = NULL; // 切断链表 [head, slow] [slow->next, 末尾]
        return mid;
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
            
        ListNode* mid = getMid(head);    // 将链表切成2半：[head, mid] [mid->next, 尾部节点]
        ListNode* L1 = sortList(head);   // 左递归(划分)：[head, NULL)
        ListNode* L2 = sortList(mid);    // 右递归(划分)：[mid, NULL)
        return merge(L1, L2);            // 合并：[left, mid] [mid->next, right] 
    }
};
```

---

# 4. 应用题

### 4.1. [146]. LRU 缓存

```go
type DLinkedNode struct {
	prev *DLinkedNode
	next *DLinkedNode
	key  int
	val  int
}

func NewDLinkedNode(key int, val int) *DLinkedNode {
	return &DLinkedNode{
		key: key,
		val: val,
	}
}

type LRUCache struct {
	// 双向链表
	head           *DLinkedNode
	tail           *DLinkedNode
	size, capacity int
	// 哈希表
	hash map[int]*DLinkedNode
}

func Constructor(capacity int) LRUCache {
	head := NewDLinkedNode(0, 0)
	tail := NewDLinkedNode(0, 0)
	head.next = tail
	// head.prev = tail
	// tail.next = head
	tail.prev = head
	return LRUCache{
		head:     head,
		tail:     tail,
		size:     0,
		capacity: capacity,
		hash:     make(map[int]*DLinkedNode),
	}
}

func (this *LRUCache) Get(key int) int {
	node, ok := this.hash[key]
	if !ok {
		return -1
	}
	this.moveToHead(node)
	return node.val
}

func (this *LRUCache) Put(key int, value int) {
	node, ok := this.hash[key]
	if ok { // 存在
		node.val = value
		this.moveToHead(node)
		return
	} else { // 不存在
		// 头插
		this.size++
		newNode := NewDLinkedNode(key, value)
		this.insertToHead(newNode)
		this.hash[key] = newNode
		if this.size > this.capacity {
			// 尾删
			this.size--
			deleteNode := this.deleteTail()
			delete(this.hash, deleteNode.key)
		}
	}
}

func (this *LRUCache) deleteNode(node *DLinkedNode) {
	prev := node.prev
	post := node.next
	prev.next = post
	post.prev = prev
}

func (this *LRUCache) insertToHead(node *DLinkedNode) {
	post := this.head.next
	this.head.next = node
	node.prev = this.head
	node.next = post
	post.prev = node
}

func (this *LRUCache) moveToHead(node *DLinkedNode) {
	this.deleteNode(node)
	this.insertToHead(node)
}

func (this *LRUCache) deleteTail() *DLinkedNode {
	deleteNode := this.tail.prev
	this.deleteNode(deleteNode)
	return deleteNode
}
```
