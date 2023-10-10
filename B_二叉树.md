- 后序遍历(非递归)


# 1. 遍历（模板）

## 1.1. 前序 

[144] 二叉树的前序遍历

递归

```c++
class Solution {
public:
    vector<int> ans;
    vector<int> preorderTraversal(TreeNode* root) {
       if (!root)
            return ans;
       ans.push_back(root->val);
       preorderTraversal(root->left);
       preorderTraversal(root->right);
       return ans;
    }
};
```

非递归

1. 初始化：栈、指针p(指向二叉树的根)
2. 循环退出条件：栈为空 || p
3. 左子树一直入栈; 当无左子树时，弹出栈顶元素，转而操作栈顶元素的右子树

```c++
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> ans;
        if(!root) return ans;

        stack<TreeNode*> S;
        TreeNode* p = root;

        while(!S.empty() || p) {
            // 左子树一直进栈
            while (p) {
                S.push(p); ans.push_back(p->val);
                p = p->left;
            }
            // 不存在左子树了，弹出栈顶元素，p指向右子树
            TreeNode* top = S.top(); S.pop();
            p = top->right;
        }
        return ans;
    }
};
```

## 1.2. 中序

[94] 二叉树的中序遍历

递归

```c++
class Solution {
public:
    vector<int> ans;
    vector<int> inorderTraversal(TreeNode* root) {
       if (!root)
            return ans;
       inorderTraversal(root->left);
       ans.push_back(root->val);
       inorderTraversal(root->right);
       return ans;
    }
};
```

非递归

- 写法与前序遍历非递归一样，唯一的差异是：保存结果的位置

```c++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> ans;
        if(!root) return ans;

        stack<TreeNode*> S;
        TreeNode* p = root;

        while (!S.empty() || p) {
            // 左子树一直进栈
            while (p) {
                S.push(p);
                p = p->left;
            }
            // 不存在左子树了，弹出栈顶元素，p指向右子树
            TreeNode* top = S.top(); S.pop(); ans.push_back(top->val);
            p = top->right;
        }
        return ans;
    }
};
```

## 1.3. 后序 😄

两个栈，实现后序遍历

```c++
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        if(!root)
            return {};

        vector<int> ans;
        
        // 两个栈, 实现后序遍历
        stack<TreeNode*> S;
        stack<TreeNode*> Shelp;

        // 初始时, 先将根入栈S
        S.push(root); 
        while(!S.empty()){  // 循环条件: S不空
            TreeNode* top = S.top(); S.pop();  // top出栈
            Shelp.push(top);                   // top入栈Shelp

            if(top->left)                      // top出栈后, 要将top的左右孩子入栈S
                S.push(top->left);
            if(top->right)
                S.push(top->right);
        }

        // 最后, Shelp中保存的元素, 就是后序遍历的结果
        while(!Shelp.empty()) {
            ans.push_back(Shelp.top()->val);
            Shelp.pop();
        }
        return ans;
    }
};
```

## 1.4. 层序

[102] 二叉树的层序遍历

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        
        vector<vector<int>> ans;

        if (!root)
            return ans;

        queue<TreeNode*> Q;
        Q.push(root);

        while (!Q.empty()) {
            vector<int> level;
            int qsize = Q.size(); // 当前队列中的元素，就是本层的所有元素
            while (qsize--) {
                TreeNode* cur = Q.front(); Q.pop();
                level.push_back(cur->val);
                if (cur->left)
                    Q.push(cur->left);
                if (cur->right)
                    Q.push(cur->right);
            }
            ans.push_back(level);
        }

        return ans;
    }
};
```

---

# 2. 构造二叉树 | 序列化/反序列化

## 2.1. 前中/中后，构造二叉树

### 2.1.1. 😄 [105] 从前序与中序遍历序列构造二叉树


前: 根左右

中: 左根右

结论: 前序遍历的数组第一个元素代表的即为根节点

1. 利用已知的根节点信息在中序遍历的数组中找到根节点所在的下标idx
2. 根据idx将中序遍历的数组分成左右两部分，左边部分即左子树，右边部分为右子树，针对每个部分可以用同样的方法继续递归下去构造


```c++
class Solution {
public:
    map<int, int> map; // key,val = 中序遍历的值, 下标

    TreeNode* build(
        vector<int>& preorder, int l_preorder, int r_preorder,
        vector<int>& inorder, int l_inorder, int r_inorder
    ) {
        if (l_preorder > r_preorder || l_inorder > r_inorder) {
            return nullptr;
        }

        int root_val = preorder[l_preorder]; // 根节点: 前序遍历的第一个节点
        int inorder_idx = map[root_val];     // 根节点将中序遍历分为2段

        int lcnt = inorder_idx - l_inorder;  // 左段元素个数
        int rcnt = r_inorder - inorder_idx;  // 右段元素个数

        TreeNode* root = new TreeNode(root_val);
        root->left = build(preorder, l_preorder+1, l_preorder+lcnt, inorder, l_inorder, inorder_idx-1);
        root->right = build(preorder, l_preorder+lcnt+1, r_preorder, inorder, inorder_idx+1, r_inorder);
        
        return root;    
    }
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        // 构造中序遍历哈希表
        for (int i=0; i<inorder.size(); i++){
            map[inorder[i]] = i;
        }
        // 创建二叉树
        return build(preorder, 0, preorder.size()-1, inorder, 0, inorder.size()-1);
    }
};
```

### 2.1.2. 😄 [106] 从中序与后序遍历序列构造二叉树

后: 左右根 

中: 左根右

结论: 后序遍历的数组最后一个元素代表的即为根节点

1. 利用已知的根节点信息在中序遍历的数组中找到根节点所在的下标idx
2. 根据idx将中序遍历的数组分成左右两部分，左边部分即左子树，右边部分为右子树，针对每个部分可以用同样的方法继续递归下去构造

```c++
class Solution {
public:
    map<int, int> map;

    TreeNode* build(
        vector<int>& inorder, int l_inorder, int r_inorder,
        vector<int>& postorder, int l_postorder, int r_postorder
    ) {
        if (l_inorder > r_inorder || l_postorder > r_postorder) 
            return nullptr;

        int root_val = postorder[r_postorder]; // 根节点: 后序遍历的最后一个节点
        int inorder_idx = map[root_val];       // 根节点将中序遍历分为2段

        int lcnt = inorder_idx - l_inorder;    // 左段元素个数
        int rcnt = r_inorder - inorder_idx;    // 右段元素个数

        TreeNode* root = new TreeNode(root_val);
        root->left = build(inorder, l_inorder, inorder_idx-1, postorder, l_postorder, l_postorder+lcnt-1);
        root->right = build(inorder, inorder_idx+1, r_inorder, postorder, l_postorder+lcnt, r_postorder-1);

        return root;
    }
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        // 构造中序遍历哈希表
        for (int i=0; i<inorder.size(); i++) {
            map[inorder[i]] = i;
        }
        // 创建二叉树
        return build(inorder, 0, inorder.size()-1, postorder, 0, postorder.size()-1);
    }
};
```

## 2.2. 构造二叉树

## 2.2.1. 😭[108] 将有序数组转换为二叉搜索树

给你一个整数数组 nums ，其中元素已经按 升序 排列，请你将其转换为一棵 高度平衡 二叉搜索树。

高度平衡 二叉树是一棵满足「每个节点的左右两个子树的高度差的绝对值不超过 1 」的二叉树。


解题关键：每次都选择整数数组nums的中间节点作为根，就能保证树是平衡的

```c++
class Solution {
public:
    TreeNode* build(vector<int>& nums, int left, int right) {
        if (left > right)  
            return nullptr;
        
        int idx = (left + right) / 2; // 每次都选择中间节点作为根
        
        TreeNode* root = new TreeNode(nums[idx]);
        root->left = build(nums, left, idx-1);
        root->right = build(nums, idx+1, right);
        
        return root;
    }
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return build(nums, 0, nums.size()-1);
    }
};
```

---

# 3. 前序遍历 (应用)

### 3.1. [104] 二叉树的最大深度

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (!root)
            return 0;
        return 1 + max(maxDepth(root->left), maxDepth(root->right));
    }
};
```

### 3.2. [111] 😭 二叉树的最小深度

- 后序遍历的应用

解决步骤: 
1. 先计算左子树和右子树的minDepth
2. 根据结果，获取整棵树的minDepth

```c++
class Solution {
public:
    int minDepth(TreeNode* root) {
        if (!root)
            return 0;
        
        int L = minDepth(root->left);
        int R = minDepth(root->right);

        if (L > 0 && R > 0) { // √ √ --> 有两个子树
            return 1 + min(L, R);
        } else { // x x , √ ×, × √   --> 左右子树，全部为空树 or 有一个为空树
            return 1 + L + R;
        }
    }
};
```

### 3.3. [110] 平衡二叉树

给定一个二叉树，判断它是否是高度平衡的二叉树。

一棵高度平衡二叉树定义为：一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1 

```c++
class Solution {
public:
    int depth(TreeNode* root) {
        if (!root)
            return 0;
        return 1+max(depth(root->left), depth(root->right));
    }

    bool isBalanced(TreeNode* root) {
        if (!root)
            return true;

        int L = depth(root->left);
        int R = depth(root->right);

        if (abs(L-R) >1) {
            return false;
        }

        return isBalanced(root->left) && isBalanced(root->right);
    }
};
```

### 3.4. [101] 😭 对称二叉树

给你一个二叉树的根节点 root ， 检查它是否轴对称



```c++
class Solution {
public:
    // 判断两棵树是否对称
    bool isSymme(TreeNode* t1, TreeNode* t2) {
        if (!t1 && !t2) return true; // × ×
        else if (t1 && t2) return t1->val == t2->val && isSymme(t1->left, t2->right) && isSymme(t1->right, t2->left); // √ √
        else return false;
    }

    bool isSymmetric(TreeNode* root) {
        if (!root) return true;
        return isSymme(root->left, root->right);
    }
};
```

### 3.5. [100] 相同的树

```c++
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (!p && !q) return true; // × ×
        else if (p && q) return p->val == q->val && isSameTree(p->left, q->left) && isSameTree(p->right, q->right); // √ √
        else return false;
    }
};
```

### 3.6. [572] 另一个树的子树

给你两棵二叉树 root 和 subRoot 。检验 root 中是否包含和 subRoot 具有相同结构和节点值的子树。如果存在，返回 true ；否则，返回 false 。


```c++
class Solution {
public:
    bool isSame(TreeNode* t1, TreeNode* t2) {
        if (!t1 && !t2) return true;
        else if (t1 && t2) return t1->val == t2->val && isSame(t1->left, t2->left) && isSame(t1->right, t2->right);
        else return false;
    }

    bool isSubtree(TreeNode* root, TreeNode* subRoot) {
        if (!root) return false;
        return isSame(root, subRoot) || isSubtree(root->left, subRoot) || isSubtree(root->right, subRoot);
    }
};
```

### 3.7. [判断: T1，T2 互为同构树? — 腾讯](https://blog.csdn.net/weixin_43088751/article/details/104079228)

T的左右子树交换任意次 ==> T'

```c++
bool dfs(TreeNode* T1, TreeNode* T2) {
    if (!T1 && !T2)      // × ×
        return true;
    else if (T1 && T2)   // √ √
        return T1->val == T2->val && (
               ( dfs(T1->left, T2->left) && dfs(T1->right, T2->right) ) ||  // 左=左 && 右 = 右
               ( dfs(T1->left, T2->right) && dfs(T1->right, T2->left) )     // 左=右 && 右=左
        );
    else                 // √ ×, × √
        return false;
}
```

### 3.8. [226] 翻转二叉树

```c++
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (!root)
            return root;
        
        // 交换左右子树
        TreeNode* tmp = root->left;
        root->left = root->right;
        root->right = tmp;
        
        invertTree(root->left);
        invertTree(root->right);
        
        return root;
    }
};
```

### 3.9. [404] 左叶子之和

```c++
class Solution {
public:
    int ans;
    int sumOfLeftLeaves(TreeNode* root) {
        if (!root) return 0;

        // 左叶子
        if (root->left && !root->left->left && !root->left->right) {
            ans += root->left->val;
        }

        sumOfLeftLeaves(root->left);
        sumOfLeftLeaves(root->right);
        
        return ans;
    }
};
```

### 3.10. [222] 完全二叉树的节点个数

```c++
class Solution {
public:
    int depth(TreeNode* root) {
        if (!root) return 0;
        return 1 + max(depth(root->left), depth(root->right));
    }
    int countNodes(TreeNode* root) {
        if (!root) return 0;

        int L = depth(root->left);
        int R = depth(root->right);

        if (L == R) {
            return countNodes(root->right) + (1<<L);
        } else {
            return countNodes(root->left) + (1<<R);
        }
    }
};
```

---

# 4. 中序遍历 (应用) 

- 适用场景：BST

解题模板

```C++
TreeNode* pre; // 前驱节点

void inorder(TreeNode* root) {
    if(!root)
        return;
    inorder(root->left);

    {
        /* -- work code -- */
    }
    pre = root;

    inorder(root->right);
}
```



### 4.1. [98. 判断二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/) 

```c++
class Solution {
public:
    bool isBST = true;
    
    TreeNode* pre = nullptr; // 前驱
    void inOrder(TreeNode* root) {
        if (!root)
            return;

        inOrder(root->left);

        if (pre) {
            if (pre->val >= root->val) {
                isBST = false;
            }
        }
        pre = root;

        inOrder(root->right);
    }

    bool isValidBST(TreeNode* root) {
        inOrder(root);
        return isBST;
    }
};
```

### 4.2. [530. 二叉搜索树的最小绝对差](https://leetcode-cn.com/problems/minimum-absolute-difference-in-bst/) 

分析：二叉搜索树任意两个节点的最小绝对值差，一定是中序遍历相邻两个节点的差

```c++
class Solution {
public:
    int ans = INT_MAX;

    TreeNode* pre = nullptr;
    void inOrder(TreeNode* root) {
        if (!root) return;

        inOrder(root->left);

        if (pre) {
            ans = min( abs(pre->val - root->val), ans );
        }
        pre = root;

        inOrder(root->right);
    }

    int getMinimumDifference(TreeNode* root) {
        inOrder(root);
        return ans;
    }
};
```

### 4.3. [783] 二叉搜索树节点最小距离

```c++
class Solution {
public:
    int ans = INT_MAX;
    TreeNode* pre = nullptr;
    
    void inOrder(TreeNode* root) {
        if (!root) 
            return;

        inOrder(root->left);

        if (pre) {
            ans = min(ans, abs(pre->val - root->val));
        }
        
        pre = root;

        inOrder(root->right);
    }
    
    int minDiffInBST(TreeNode* root) {
        inOrder(root);
        return ans;
    }
};
```

### 4.4. [面试题 17.12. BiNode](https://leetcode-cn.com/problems/binode-lcci/) :small_airplane: 

- 二叉搜索树转换为**单向链表**，保持链表单调性

二叉树数据结构TreeNode可用来表示单向链表（其中left置空，right为下一个链表节点）。实现一个方法，把二叉搜索树转换为单向链表，要求依然符合二叉搜索树的性质，转换操作应是原址的，也就是在原始的二叉搜索树上直接修改。返回转换后的单向链表的头节点。

```c++
class Solution {
    TreeNode* head = NULL; // 链表的头结点
    TreeNode* pre = NULL;
public:
    void inorder(TreeNode* root) {
        if (!root)
            return;

        inorder(root->left);

        if (!pre) {
            pre = root;
            head = pre;
        } else {
            pre->right = root;
            pre->left = NULL;
            pre = root;
        }

        inorder(root->right);
    }
    TreeNode* convertBiNode(TreeNode* root) {
        if (!root) 
            return NULL;

        inorder(root);
        
        // 最后一个节点设置为NULL
        pre->left = NULL;
        pre->right = NULL;
        
        return head;
    }
};
```

补充：[114] 二叉树展开为链表

1. 前序遍历二叉树，将结果保存在vec中
2. 遍历vec，串连成一个链表

```c++
class Solution {
public:
    vector<TreeNode*> vec;
    void preorderTraversal(TreeNode* root) {
        if (!root) return;
        vec.push_back(root);
        preorderTraversal(root->left);
        preorderTraversal(root->right);
    }

    void flatten(TreeNode* root) {
        // 前序遍历
        preorderTraversal(root);

        // 遍历前序遍历的结果集，串联成一个链表
        int n = vec.size();
        for (int i = 0; i < n-1; i++) {
            TreeNode *pre = vec[i];
            TreeNode *cur = vec[i+1];
            pre->left = nullptr;
            pre->right = cur;
        }
    }
};
```


### 4.5. [面试题 04.06. 后继者](https://leetcode-cn.com/problems/successor-lcci/) 

设计一个算法，找出二叉搜索树中指定节点的“下一个”节点（也即中序后继）。

如果指定节点没有对应的“下一个”节点，则返回null。



- 递归解法(最优解): 结合BST树单调性的特点

```c++
class Solution {
    TreeNode* pre = NULL;
    TreeNode* ans = NULL;
public:
    void inorder(TreeNode* root, TreeNode* p) {
        if (!root) 
            return;

        inorder(root->left, p);

        if (!pre) {
            pre = root;
        } else {
            if (pre == p) {
                ans = root;
            }
            pre = root;
        }
        
        inorder(root->right, p);
    }
    TreeNode* inorderSuccessor(TreeNode* root, TreeNode* p) {
        if (!root) return NULL;

        inorder(root, p);
        return ans;
    }
};
``` 

### 4.6. 😄 [230. 二叉搜索树中第K小的元素](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/) 

```c++
class Solution {
    TreeNode* pre = nullptr;
    TreeNode* ans = nullptr;
    int kk;
public:
    void inorder(TreeNode* root) {
        if (!root) 
            return;

        inorder(root->left);

        pre = root;
        
        kk--;
        if (kk == 0) {
            ans = pre;
            return;
        }

        inorder(root->right);
    }
    int kthSmallest(TreeNode* root, int k) {
        kk = k;
        inorder(root);
        return ans->val;
    }
};
```

### 4.7. 😄 [99] 恢复二叉搜索树

错误写法 ❎

- 思想：找到一个地方不符合BST，直接交换，然后返回

- 剖析：这种写法，会存在漏掉case的场景 

```c++
class Solution {
public:

    TreeNode* pre = nullptr;
    void inOrder(TreeNode* root) {
        if (!root)
            return;
        
        inOrder(root->left);

        if (pre) {
            if (pre->val > root->val) {
                int tmp = pre->val;
                pre->val = root->val;
                root->val = tmp;
                return;
            }
        }
        pre = root;

        inOrder(root->right);
    }

    void recoverTree(TreeNode* root) {
        inOrder(root);
    }
};
```

正确写法：✅

1. 找到不符合BST的节点：node1、node2（node1找到了就不再更新了，node2会一直更新）
2. 交接node1和node2

```c++
class Solution {
public:
    TreeNode* node1 = nullptr; 
    TreeNode* node2 = nullptr;

    TreeNode* pre = nullptr; // 前驱
    void inOrder(TreeNode* root) {
        if (!root)
            return;

        inOrder(root->left);
        
        {
            // node1只更新一次，node2每次都更新
            if (pre && pre->val >= root->val) {
                if (!node1){
                    node1 = pre;        
                }
                node2 = root;
            }
        }

        pre = root;

        inOrder(root->right);
    }

    void recoverTree(TreeNode* root) {
        inOrder(root);

        int tmp = node1->val;
        node1->val = node2->val;
        node2->val = tmp;
    }
};
```

### 4.8. 😄 [501] 二叉搜索树中的众数

这个题竟然是easy

核心难点：answer的更新

```
class Solution {
public:
    vector<int> answer; // 保存众数的结果集
        
    int maxCnt = 0;
    int curCnt = 0;

    TreeNode* pre = nullptr;

    void inOrder(TreeNode* root) {
        if (!root) 
            return;
        
        inOrder(root->left);

        
        if (!pre) {
            curCnt = 1;
        } else {
            if (pre->val == root->val) {
                curCnt++;
            } else {
                curCnt = 1;
            }
        }

        /* 核心: answer的更新 */
        if (curCnt == maxCnt) { // 当相等时，直接插入
            answer.push_back(root->val);
        }
        if (curCnt > maxCnt) {  // 当变更多时，清除answer，将当前值root->val插入
            answer = vector<int> {root->val};
        }

        maxCnt = max(maxCnt, curCnt);

        pre = root;

        inOrder(root->right);
    }

    vector<int> findMode(TreeNode* root) {
        inOrder(root);
        return answer;
    }
};
```

---

# 5. 后序遍历 (递归应用)

1. 整棵树 ==> 每个节点的(大问题) ==> 每个节点的(小问题) 
2. 写出小问题的实现函数
3. 在小问题的实现函数中加几行代码

```c++

int small(TreeNode* root) {
    int L = small(root->left);
    int R = small(root->right);

    // 每个节点的大问题 = 表达式(L,R)

    return 小问题的题解，使用到L,R;
}
```


### 5.1. :small_airplane: [543. 二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)  

```c++
          1
         / \
        2   3
       / \     
      4   5    
返回 3, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。
注意：两结点之间的路径长度是以它们之间边的数目表示。
```

分析: 以root为根的二叉树的直径 = height(root->left) + height(root->right)

```c++
class Solution {
public:
    int diameter; // 直径 = 左子树高度 + 右子树高度

    int depth(TreeNode* root) {
        if (!root)
            return 0;

        int L = depth(root->left);
        int R = depth(root->right);

        // cout << root->val << ' ' << L + R << endl; // 以root为根的最大直径
        diameter = max(diameter, L + R);
        
        return 1 + max(L, R);
    }

    int diameterOfBinaryTree(TreeNode* root) {
        depth(root);
        return diameter;
    }
};
```


### 5.2. :small_airplane: [563. 二叉树的坡度](https://leetcode-cn.com/problems/binary-tree-tilt/) 

一个树的`每个节点root的坡度`定义即为，该节点左子树的节点之和与右子树节点之和的`差的绝对值`，即，abs( 节点数和(root->left) - 节点数和(root->right) )

二叉树的坡度 = 整棵树的每个节点的坡度的总和

```
       1               2
      / \             / \
     2   3   ===>    2   0
    / \             / \
   3   5           0   0
```

```c++
class Solution {
public:
    int ans = 0;
    int count(TreeNode* root) { // count递归函数的定义: 以root为根的节点值的和
        if(!root)
            return 0;

        int Lcount = count(root->left);
        int Rcount = count(root->right);

        cout << root->val << ' ' << abs(Lcount-Rcount) << endl; // 打印每个节点的坡度
        ans += abs(Lcount-Rcount);

        return root->val + Lcount + Rcount;
    }
    int findTilt(TreeNode* root) {
        count(root);
        return ans;
    }
};
```

### 5.3. 😄 [124. 二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/) (hard)(字节)


给定一个非空二叉树，返回其最大路径和。

本题中，路径被定义为一条从树中任意节点出发，沿父节点-子节点连接，达到任意节点的序列。该路径至少包含一个节点，【且不一定经过根节点】。


```c++
输入：[-10,9,20,null,null,15,7]
   -10
   / \
  9  20
    /  \
   15   7
输出：42 = 15 + 20 + 7
```

```c++
class Solution {
public:
    int ans = INT_MIN;
    
    // maxGain: 计算二叉树中一个节点的最大贡献值，即以该节点为根，寻找“一条”路径，使得该路径上的节点值之和最大
    int maxGain(TreeNode* root) {
        if (!root)
            return 0;

        int lMaxGain = max( maxGain(root->left), 0 );
        int rMaxGain = max( maxGain(root->right), 0 );

        // 二叉树中的最大路径和
        ans = max(ans, root->val + lMaxGain + rMaxGain);

        return root->val + max(lMaxGain, rMaxGain); // 以root为根的最大贡献值 = root->val + max(lMaxGain, rMaxGain)
    }

    int maxPathSum(TreeNode* root) {
        maxGain(root);
        return ans;
    }
};
```


---


# 6.层序遍历（应用）

### 6.1. 二叉树的右视图

```c++
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> ans;
        
        if (!root)
            return ans;
        
        queue<TreeNode*> Q;
        Q.push(root);

        while (!Q.empty()) {
            int qsize = Q.size();
            while (qsize--) {
                TreeNode* cur = Q.front(); Q.pop();
                if (qsize == 0) {
                    ans.push_back(cur->val);
                }
                if (cur->left)
                    Q.push(cur->left);
                if (cur->right)
                    Q.push(cur->right);
            }
        }

        return ans;
    }
};
```

### 6.2. [513] 找树左下角的值

```c++
class Solution {
public:
    int findBottomLeftValue(TreeNode* root) {
        if(!root)
            return -1;
        queue<TreeNode*> Q;
        Q.push(root);

        int ans = 0;
        while (!Q.empty()) {
            int size = Q.size();
            bool flag = false;
            while (size--) {
                TreeNode* cur = Q.front(); Q.pop();
                if (!flag) {
                    ans = cur->val;
                    flag = true;
                }
                if (cur->left) Q.push(cur->left);
                if (cur->right) Q.push(cur->right);
            }
        }
        return ans;
    }
};
```


### 6.3. [103] 二叉树的锯齿形层序遍历

```c++
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {

        vector<vector<int>> ans;

        if (!root)
            return ans;

        queue<TreeNode*> Q;
        Q.push(root);


        int i = 1;

        while (!Q.empty()) {
            vector<int> level;
            int size = Q.size();
            while (size--) {
                TreeNode* cur = Q.front(); Q.pop();
                level.push_back(cur->val);
                
                if (cur->left)
                    Q.push(cur->left);
                if (cur->right)
                    Q.push(cur->right);
            }

            if (i < 0) { 
                std::reverse(level.begin(), level.end()); // vector反转，采用std::reverse
            }
            i = -i;


            ans.push_back(level);
        }

        return ans;
    }
};
```


### 6.4. 😄[662] 二叉树最大宽度

层序遍历，queue中保存的元素是 <TreeNode*, index>

```cpp
struct QNode {
    TreeNode* node;
    unsigned int idx;
    QNode(TreeNode* node, unsigned int idx) : node(node), idx(idx) {}
};

class Solution {
public:
    int widthOfBinaryTree(TreeNode* root) {
        if (!root)
            return 0;
        
        unsigned int ans;

        queue<QNode*> Q;
        Q.push(new QNode(root, 1));

        while (!Q.empty()) {
            int size = Q.size();
            bool flag = false;
            unsigned int leftIndex;
            while (size--) {
                QNode* cur = Q.front(); Q.pop();
                if (!flag) {
                    leftIndex = cur->idx;
                    flag = true;
                }
                if (cur->node->left) {
                    Q.push(new QNode(cur->node->left, 2*cur->idx));
                }
                if (cur->node->right) {
                    Q.push(new QNode(cur->node->right, 2*cur->idx+1));
                }
                ans = max(ans, cur->idx - leftIndex + 1);
            }
        }

        return int(ans);
    }
};
```

### 6.5. [958. 二叉树的完全性检验](https://leetcode-cn.com/problems/check-completeness-of-a-binary-tree/)

判断一棵树是否为完全二叉树

```c++
class Solution {
public:
    bool isCompleteTree(TreeNode* root) {
        if (!root)
            return true;
        queue<TreeNode*> Q;
        Q.push(root);

        while (!Q.empty()) {
            TreeNode* cur = Q.front(); Q.pop();
            if (cur) {
                Q.push(cur->left);
                Q.push(cur->right);
            } else { // 遇到一个空节点，就退出循环
                break;
            }
        }
        // 检查队列Q中是否有非空节点
        while (!Q.empty()) {
            TreeNode* cur = Q.front(); Q.pop();
            if (cur) { // 若非空，则不是完全二叉树
                return false;
            }
        }
        return true;
    }
};
```

### 6.6. 填充每个节点的下一个右侧节点指针 


层序遍历的变种题

- [116] 填充每个节点的下一个右侧节点指针 
- [117] 填充每个节点的下一个右侧节点指针 II

说明：这两个题目，用下面的同一个代码都可以解决

```c++
class Solution {
public:
    Node* connect(Node* root) {
        if (!root)
            return root;

        queue<Node*> Q;
        Q.push(root);

        while(!Q.empty()) {
            int size = Q.size();
            Node* pre = NULL;
            while (size--) {
                Node* cur = Q.front(); Q.pop();
                if (!pre) {
                    pre = cur;
                } else {
                    pre->next = cur;
                    pre = cur;
                }
                if (size == 0) {
                    cur->next = NULL;
                }
                if (cur->left) Q.push(cur->left);
                if (cur->right) Q.push(cur->right);
            }
        }

        return root;
    }
};
```



---

# 7. 最近的公共祖先

### 7.1. :small_airplane: 在二叉树中查找指定节点  

在引出二叉树的公共祖先之前，先介绍`在二叉树中查找指定节点`，代码见下：

```c++
TreeNode* find(TreeNode* root, TreeNode* p) {
    if (!root)      // 树为空\查找到NULL依旧没查找到, 就返回NULL
        return NULL;

    if (root == p)  // 当前节点查找到, 返回查找到的结果
        return root;

    TreeNode* L = find(root->left, p);  // 去左子树查询
    if (L)
        return L;
    
    TreeNode* R = find(root->right, p); // 去右子树查询
    if (R)
        return R;
    
    return NULL;  // root\L\R中都没查找到, 就返回NULL
}
```

### 7.2. 😄 [236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/) 

题意: p,q是树上的节点

```c++
class Solution {
    // 判断 p或q 是否在在树root上（只要有一个在root，就返回true）
    bool find(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root) {
            return NULL;
        }
        return root == p || root == q || find(root->left, p, q) || find(root->right, p, q);
    }
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root == p || root == q) {
            return root;
        }
        bool lflag = find(root->left, p, q);
        bool rflag = find(root->right, p, q);
        if (lflag && rflag) { // √ √
            return root;
        } else if (!lflag && !rflag) { // X X
            return NULL;
        } else if (lflag && !rflag) { // √ X
            return lowestCommonAncestor(root->left, p, q);
        } else { // X √
            return lowestCommonAncestor(root->right, p, q);
        }
    }
};
```

### 7.3. [235. 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)


```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || !p || !q)
            return nullptr;
        
        if (p->val > root->val && q->val > root->val) {        // p,q > root, 查找右子树
            return lowestCommonAncestor(root->right, p, q);
        } else if (p->val < root->val && q->val < root->val) { // p,q < root, 查找左子树
            return lowestCommonAncestor(root->left, p, q);
        } else {                                               // 一个大, 一个小, 返回root
            return root;
        }
    }
};
```


---

# 8. 路径 

### 8.1. [112.路径总和](https://leetcode-cn.com/problems/path-sum/) (easy)

给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        if (!root) {
            return false;
        }

        if (root && !root->left && !root->right && targetSum == root->val) { / 叶子节点
            return true;
        }

        return hasPathSum(root->left, targetSum-root->val) || hasPathSum(root->right, targetSum-root->val);
    }
};
```

### 8.2. [113.路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)

给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于给定目标和的路径。

```c++
class Solution {
    vector<vector<int>> ans;
public:
    void dfs(TreeNode* root, int targetSum, vector<int> path) {
        if (!root) 
            return;
        path.push_back(root->val);
        if (!root->left && !root->right && targetSum == root->val) {
            ans.push_back(path);
            return;
        }
        dfs(root->left, targetSum-root->val, path);
        dfs(root->right, targetSum-root->val, path);
    }
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        vector<int> path;
        dfs(root, targetSum, path);
        return ans;
    }
};
```

### 8.3. 😄[437] 路径总和 III 



### 8.4. [257.二叉树的所有路径](https://leetcode-cn.com/problems/binary-tree-paths/) (easy)

给定一个二叉树，返回所有从根节点到叶子节点的路径。

```c++
class Solution {
public:

    vector<string> allpath; 

    void dfs(TreeNode* root, string path) {
        if (!root) return;

        if (path == "") {
            path = to_string(root->val);
        } else {
            path = path + "->" + to_string(root->val);
        }
        
        if (!root->left && !root->right) {
            allpath.push_back(path);
            return;
        }

        dfs(root->left, path);
        dfs(root->right, path);
    }

    vector<string> binaryTreePaths(TreeNode* root) {
        dfs(root, "");
        return allpath;
    }
};
```

### 8.5. [129.求根节点到叶节点数字之和](https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/) (中等)

给定一个二叉树，它的每个结点都存放一个 0-9 的数字，每条从根到叶子节点的路径都代表一个数字。

例如，从根到叶子节点路径 1->2->3 代表数字 123。

计算从根到叶子节点生成的所有数字之和。

```c++
输入: [1,2,3]
    1
   / \
  2   3
输出: 25
解释:
从根到叶子节点路径 1->2 代表数字 12.
从根到叶子节点路径 1->3 代表数字 13.
因此，数字总和 = 12 + 13 = 25.
```

```c++
class Solution {
public:
    int ans;

    void dfs(TreeNode* root, int path) {
        if (!root)
            return;
        
        path = path * 10 + root->val;

        if (!root->left && !root->right) {
            ans += path;    
            return;
        }

        dfs(root->left, path);
        dfs(root->right, path);
    }

    int sumNumbers(TreeNode* root) {
        dfs(root, 0);
        return ans;
    }
};
```




