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

        TreeNode* p = root;
        stack<TreeNode*> s;

        while (p || !s.empty()) {
            while(p) { // 左子树一直进栈
                s.push(p); ans.push_back(p->val);
                p = p->left;
            }
            // 没有左子树，就出栈，然后访问栈顶的右子树
            TreeNode* top = s.top(); s.pop();
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

         TreeNode* p = root;
         stack<TreeNode*> s;

         while (p || !s.empty()) {
             while(p) { // 左子树一直进栈
                 s.push(p);
                 p = p->left;
             }
             // 没有左子树，就出栈，然后访问栈顶的右子树
             TreeNode* top = s.top(); s.pop(); ans.push_back(top->val);
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

        if (!root) return ans;

        queue<TreeNode*> Q;
        Q.push(root);

        while (!Q.empty()) {
            vector<int> level;
            int size = Q.size(); // 当前队列中的元素，就是本层的所有元素
            while (size--) {
                TreeNode* cur = Q.front(); Q.pop();
                level.push_back(cur->val);
                if (cur->left) Q.push(cur->left);
                if (cur->right) Q.push(cur->right);
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


前序+中序 \ 后序+中序: 二者的区别是【谁作为根节点】，其他代码都一样


### 2.1.1. 😄 [105] 从前序与中序遍历序列构造二叉树


前: 根左右

中: 左根右

结论: 前序遍历的数组第一个元素代表的即为根节点

1. 利用已知的根节点信息在中序遍历的数组中找到根节点所在的下标idx
2. 根据idx将中序遍历的数组分成左右两部分，左边部分即左子树，右边部分为右子树，针对每个部分可以用同样的方法继续递归下去构造


```c++
class Solution {
public:
    map<int, int> inorderMap; // (值,下标)

    TreeNode* build(
        vector<int>& preorder, int l_preorder, int r_preorder,
        vector<int>& inorder, int l_inorder, int r_inorder
    ) {
        if (l_preorder > r_preorder || l_inorder > r_inorder) {
            return NULL;
        }

        int root_val = preorder[l_preorder];      // 前序遍历的首节点，一定是根节点
        int idx = inorderMap[root_val];           // 根节点，将中序遍历分割成2半
        int leftCnt = idx-1 - l_inorder + 1;      // 左边节点的个数

        TreeNode* root = new TreeNode(root_val);
        root->left = build(preorder, l_preorder+1, l_preorder+leftCnt, inorder, l_inorder, idx-1);
        root->right = build(preorder, l_preorder+leftCnt+1, r_preorder, inorder, idx+1, r_inorder);

        return root;
    }

    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int size = preorder.size();
        for (int i=0;i<size;i++){
            inorderMap[inorder[i]] = i;
        }

        return build(preorder, 0, size-1, inorder, 0, size-1);
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
    map<int, int> inorderMap; // (值,下标)

    TreeNode* build(
        vector<int>& postorder, int l_postorder, int r_postorder,
        vector<int>& inorder, int l_inorder, int r_inorder
    ) {
        if (l_postorder > r_postorder || l_inorder > r_inorder) {
            return NULL;
        }

        int root_val = postorder[r_postorder];      // 后序遍历的首节点，一定是根节点
        int idx = inorderMap[root_val];             // 根节点，将中序遍历分割成2半
        int leftCnt = idx-1 - l_inorder + 1;        // 左边节点的个数

        TreeNode* root = new TreeNode(root_val);
        root->left = build(postorder, l_postorder, l_postorder+leftCnt-1, inorder, l_inorder, idx-1);
        root->right = build(postorder, l_postorder+leftCnt, r_postorder-1, inorder, idx+1, r_inorder);

        return root;
    }
    
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        int size = postorder.size();
        for (int i=0;i<size;i++){
            inorderMap[inorder[i]] = i;
        }

        return build(postorder, 0, size-1, inorder, 0, size-1);
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
    TreeNode* build(vector<int>& nums, int l, int r) {
        if (l > r) return NULL;

        int mid = (l+r)/2;
        TreeNode* root = new TreeNode(nums[mid]); // 每次都选择中间节点作为根
        root->left = build(nums, l, mid-1);
        root->right = build(nums, mid+1, r);
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

### 3.2. [111] 二叉树的最小深度

- 后序遍历的应用

解决步骤: 
1. 先计算左子树和右子树的minDepth
2. 根据结果，获取整棵树的minDepth

```c++
class Solution {
public:
    int depth(TreeNode* root) {
        if (!root) return 0;
        
        if (root->left && root->right) { // √ √
            return 1+min(depth(root->left), depth(root->right));
        } else { // (X,X)  (√,X)  (X,√) 
            return 1+max(depth(root->left), depth(root->right));
        }
    }
    int minDepth(TreeNode* root) {
        return depth(root);
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
        if (!root) return 0;
        return 1+max(depth(root->left), depth(root->right));
    }
    bool isBalanced(TreeNode* root) {
        if (!root) return true;

        int lh = depth(root->left);
        int rh = depth(root->right);

        if (abs(lh-rh) > 1) return false;

        return isBalanced(root->left) && isBalanced(root->right);
    }
};
```

### 3.4. [101] 对称二叉树

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

1. 先写isSameTree
2. 再写

```c++
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (!p && !q) return true;
        else if (p && q) return p->val == q->val && isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
        else return false;
    }
    bool isSubtree(TreeNode* root, TreeNode* subRoot) {
        if (!root) return false;
        return isSameTree(root, subRoot) || isSubtree(root->left, subRoot) || isSubtree(root->right, subRoot);
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

### 3.8.😄 [226] 翻转二叉树

注意：这个题目是“交换节点”，不是“交换值”

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
    int sum = 0;
public:
    void dfs(TreeNode* root) {
        if (!root) return;
        // 左叶子
        if (root->left && !root->left->left && !root->left->right) {
            sum += root->left->val;
        }
        dfs(root->left);
        dfs(root->right);
    }
    int sumOfLeftLeaves(TreeNode* root) {
        dfs(root);
        return sum;
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

        int lh = depth(root->left);
        int rh = depth(root->right);

        if (lh == rh) {
            return (1<<lh) + countNodes(root->right);
        } else {
            return (1<<rh) + countNodes(root->left);
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

    if (!pre) { // 第一次进来，pre还是null
        // do nothing
    } else {
        // do work 😄
    }
    pre = root;

    // do work 😄

    inorder(root->right);
}
```



### 4.1. [98] 判断二叉搜索树 

```c++
class Solution {
public:
    TreeNode* pre;
    bool dfs(TreeNode* root) {
        if (!root) return true;
        
        bool lf = dfs(root->left);
        if (!lf) return false;

        if (!pre) {
            // do nothing
        } else {
            if (pre->val >= root->val) {
                return false;
            }
        }

        pre = root;

        return dfs(root->right);
    }
    bool isValidBST(TreeNode* root) {
        pre = NULL;
        return dfs(root);
    }
};
```

### 4.2. [530] 二叉搜索树的最小绝对差   [783] 二叉搜索树节点最小距离

分析：二叉搜索树任意两个节点的最小绝对值差，一定是中序遍历相邻两个节点的差

```c++
class Solution {
public:
    int ans=INT_MAX;

    TreeNode* pre;
    void dfs(TreeNode* root) {
        if (!root) return;

        dfs(root->left);
        if (!pre) {
            // do nothing
        } else {
            int diff = root->val - pre->val;
            if (diff < ans) {
                ans = diff;
            }
        }

        pre = root;

        dfs(root->right);
    }
    int getMinimumDifference(TreeNode* root) {
        pre = NULL;
        dfs(root);
        return ans;
    }
};
```


### 4.4. 面试题 17.12. BiNode

- 二叉搜索树转换为**单向链表**，保持链表单调性

二叉树数据结构TreeNode可用来表示单向链表（其中left置空，right为下一个链表节点）。实现一个方法，把二叉搜索树转换为单向链表，要求依然符合二叉搜索树的性质，转换操作应是原址的，也就是在原始的二叉搜索树上直接修改。返回转换后的单向链表的头节点。

```c++
class Solution {
public:
    TreeNode* head = NULL;

    TreeNode* pre = NULL;
    void dfs(TreeNode* root) {
        if (!root) return;

        dfs(root->left);

        if (!pre) { // 之前还没有节点: 给head初始化
            head = root;
        } else {
            pre->right = root; // right链表连接
            pre->left = NULL;  // left设置为NULL
        }
        pre = root;

        dfs(root->right);
    }
    TreeNode* convertBiNode(TreeNode* root) {
        if (!root) return NULL;

        dfs(root);

        pre->left = NULL;
        pre->right = NULL;

        return head;
    }
};
```

### 4.5.😄 面试题 04.06. 后继者 

设计一个算法，找出二叉搜索树中指定节点的“下一个”节点（也即中序后继）。

如果指定节点没有对应的“下一个”节点，则返回null。

- 递归解法(最优解): 结合BST树单调性的特点

```c++
class Solution {
public:
    TreeNode* ans=NULL;
    TreeNode* pp; // 临时存放p(简化代码编写)

    TreeNode* pre=NULL;
    void dfs(TreeNode* root) {
        if (ans) return; // 如果已经找到了，直接退出

        if (!root) return;

        dfs(root->left);
        if (ans) return; // 如果已经找到了，直接退出

        if (!pre) {
            // do nothing
        } else {
            if (pre == pp) { // 当pre指向p时，则root指向p的后继
                ans = root;
                return; // 如果已经找到了，直接退出
            }
        }
        pre = root;

        dfs(root->right);
        if (ans) return; // 如果已经找到了，直接退出
    }

    TreeNode* inorderSuccessor(TreeNode* root, TreeNode* p) {
        pp = p;
        dfs(root);
        return ans;
    }
};
``` 

### 4.6.😄 [230] 二叉搜索树中第K小的元素



```c++
class Solution {
public:
    TreeNode* ans = NULL;
    TreeNode* pre = NULL;

    int kk; // 简化代码

    void dfs(TreeNode* root) {
        if (!root) return;

        dfs(root->left);

        if (!pre) {

        } else {

        }

        pre = root;
        
        if (--kk==0) {
            ans = root;
            return;
        }

        dfs(root->right);
    }
    int kthSmallest(TreeNode* root, int k) {
        kk = k;
        dfs(root);
        return ans->val;
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


### 5.1. [543] 二叉树的直径  

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
    int ans = 0;

    int depth(TreeNode* root) { // 高度
        if(!root) return 0;
        return 1 + max(depth(root->left), depth(root->right));
    }

    void dfs(TreeNode* root) {
        if(!root) return;
        // root直径 = 高度(root->left) + 高度(root->right)
        ans = max(ans, depth(root->left) + depth(root->right));
        // 左子树
        dfs(root->left);
        // 右子树
        dfs(root->right);
    }

    int diameterOfBinaryTree(TreeNode* root) {
        if(!root) return 0;
        dfs(root);
        return ans;
    }
};
```


### 5.2. [563] 二叉树的坡度 

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
    int ans=0;

    int count(TreeNode* root) { // 节点总和
        if (!root) return 0;
        return root->val + count(root->left) + count(root->right);
    }
    void dfs(TreeNode* root) {
        if (!root) return;
        // root坡度 = abs{ 节点总和(root->left) - 节点总和(root->right) }
        ans += abs( count(root->left) - count(root->right) );
        // 累加 左子树
        dfs(root->left);
        // 累加 右子树
        dfs(root->right);
    }
    int findTilt(TreeNode* root) {
        if (!root) return 0;
        dfs(root);
        return ans;
    }
};
```

### 5.3. 😭 [124] 二叉树中的最大路径和


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

    int count(TreeNode* root) { // 从当前节点出发，向下走一条链的最大和
        if (!root) return 0;

        int L = max(0, count(root->left));
        int R = max(0, count(root->right));

        return root->val + max(L, R);
    }

    void dfs(TreeNode* root) {
        if (!root) return;

        int RootVal = root->val;
        int L = max(0, count(root->left));
        int R = max(0, count(root->right));

        ans = max(ans, RootVal);
        ans = max(ans, RootVal + L);
        ans = max(ans, RootVal + R);
        ans = max(ans, RootVal + L + R);

        dfs(root->left);
        dfs(root->right);
    }

    int maxPathSum(TreeNode* root) {
        if (!root) return 0;
        dfs(root);
        return ans;
    }
};
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

### 6.1. [199] 二叉树的右视图

右视图

1. 右边
2. 层次

```c++
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> ans;
        if (!root) return ans;

        queue<TreeNode*> Q;
        Q.push(root);
        while (!Q.empty()) {
            int size = Q.size();
            while (size--) {
                TreeNode* cur = Q.front(); Q.pop();
                if (size == 0) {
                    ans.push_back(cur->val);
                }
                if (cur->left) Q.push(cur->left);
                if (cur->right) Q.push(cur->right);
            }
        }
        return ans;
    }
};
```

### 6.2. [513] 找树左下角的值

最左下角

1. 最下：层序遍历控制最下
2. 最左：flag控制变量是否是最左

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
        if (!root) return ans;

        queue<TreeNode*> Q;
        Q.push(root);

        int i = 0; // 判断是否需要翻转
        while (!Q.empty()) {
            int size = Q.size();
            vector<int> level;
            while (size--) {
                TreeNode* p = Q.front(); Q.pop();
                level.push_back(p->val);
                if (p->left) Q.push(p->left);
                if (p->right) Q.push(p->right);
            }
            if (i%2!=0) {
                reverse(level.begin(), level.end());
            }
            i++;
            ans.push_back(level);
        }

        return ans;
    }
};
```


### 6.4. 😄[662] 二叉树最大宽度

层序遍历，queue中保存的元素是 <TreeNode*, index>

- 恶心的点：index要使用 unsigned int

- 最左边的节点  l_index==0
- 最右边的节点  size==0

```cpp
struct IndexTreeNode {
    TreeNode* node;
    unsigned int index; // 下标
    IndexTreeNode(TreeNode* node, unsigned int x) : node(node), index(x) {}
};
class Solution {
public:
    int widthOfBinaryTree(TreeNode* root) {
        unsigned int ans = 0;
        if(!root) return ans;

        queue<IndexTreeNode*> Q; Q.push(new IndexTreeNode(root, 0));

        while (!Q.empty()) {
            int size = Q.size();
            unsigned int leftIndex = 0;
            unsigned int rightIndex = 0;
            while(size--) {
                IndexTreeNode* cur = Q.front(); Q.pop();
                if (leftIndex == 0) { // 该层的最左节点
                    leftIndex = cur->index;
                }
                if (size == 0) { // 该层的最右节点
                    rightIndex = cur->index;
                }
                if (cur->node->left) {
                    Q.push(new IndexTreeNode(cur->node->left, 2*cur->index+1));
                }
                if (cur->node->right) {
                    Q.push(new IndexTreeNode(cur->node->right, 2*cur->index+2));
                }
            }
            ans = max(ans, rightIndex-leftIndex+1);
        }
        return ans;
    }
};
```

### 6.5. [958] 二叉树的完全性检验

判断一棵树是否为完全二叉树

```c++
// 模板写法

class Solution {
public:
    bool isCompleteTree(TreeNode* root) {
        if (!root) return true;

        queue<TreeNode*> Q;
        Q.push(root);

        while (!Q.empty()) {
            int size = Q.size();
            while (size--) {
                TreeNode* cur = Q.front(); Q.pop();
                if (!cur) { // 空节点，退出 (判断queue中是否有 非nil 节点)
                    goto end;
                }
                Q.push(cur->left);  // (即使左右子树=nil)，也将 --> 左右子树直接进queue
                Q.push(cur->right); // (即使左右子树=nil)，也将 --> 左右子树直接进queue
            }
        }

end:
        while (!Q.empty()) {
            TreeNode* cur = Q.front(); Q.pop();
            if (cur) return false; // 存在 非nil 节点 --> 不是完全二叉树
        }

        return true;
    }
};

```

```c++
// 简单写法

class Solution {
public:
    bool isCompleteTree(TreeNode* root) {
        if (!root) return true;
        
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
        if(!root) return root;

        queue<Node*> Q;
        Q.push(root);

        while (!Q.empty()) {
            int size = Q.size();
            Node* pre = NULL;

            while(size--) {
                Node* cur = Q.front(); Q.pop();

                if (!pre) {
                    pre = cur;
                } else {
                    pre->next = cur; // 连接
                    pre = cur;
                }

                if (cur->left) {
                    Q.push(cur->left);
                }
                if (cur->right) {
                    Q.push(cur->right);
                }
            }
        }

        return root;
    }
};
```



---

# 7. 最近的公共祖先



### 7.1. 😄 [236] 二叉树的最近公共祖先 


```c++
class Solution {
public:
    TreeNode* dfs(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root==p || root==q) return root;

        TreeNode* L = dfs(root->left, p, q);
        TreeNode* R = dfs(root->right, p, q);
        if (L && R) return root; // p\q分别在root的左右子树 ---> root就是最近公共祖先
        return L ? dfs(L, p, q) : dfs(R, p, q); // p\q都在左子树上 ? [去左子树查] : [去右子树查]
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root==p || root==q) return root;
        return dfs(root, p, q);
    }
};
```

### 7.2. [235] 二叉搜索树的最近公共祖先

- 因为是二叉搜索树BST🌲，所以，使用 root、p\q 的值对比


```c++
class Solution {
public:
    TreeNode* dfs(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root==p || root==q) return root;

        if (root->val > p->val && root->val > q->val) { // root > p\q --> 去左子树查
            return dfs(root->left, p, q);
        } else if (root->val < p->val && root->val < q->val) { // root < p\q --> 去右子树查
            return dfs(root->right, p, q);
        } else { // root在p\q之间 --> root就是最近公共祖先
            return root;
        }
    }
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if (!root || root==p || root==q) return root;
        return dfs(root, p, q);
    }
};
```


---

# 8. 路径 

### 8.1. [112] 路径总和

给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。

```c++
class Solution {
public:
    bool dfs(TreeNode* root, int targetSum) {
        if (!root) return false;
        if (!root->left && !root->right && root->val == targetSum) { // 叶子节点
            return true;
        }
        return dfs(root->left, targetSum-root->val) || dfs(root->right, targetSum-root->val);
    }
    bool hasPathSum(TreeNode* root, int targetSum) {
        if (!root) return false;
        return dfs(root, targetSum);
    }
};
```

### 8.2. [113] 路径总和 II

给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于给定目标和的路径。

```c++
class Solution {
    vector<vector<int>> ans;
public:
    void dfs(TreeNode* root, vector<int> path, int targetSum) {
        if (!root) return;
        path.push_back(root->val);
        if (!root->left && !root->right && root->val == targetSum) {
            ans.push_back(path);
        }
        dfs(root->left, path, targetSum-root->val);
        dfs(root->right, path, targetSum-root->val);
    }
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        if (!root) return ans;
        vector<int> path;
        dfs(root, path, targetSum);
        return ans;
    }
};
```

### 8.3. 😄[437] 路径总和 III 



### 8.4. [257] 二叉树的所有路径

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

### 8.5. [129] 求根节点到叶节点数字之和

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
    int ans = 0;
public:
    void dfs(TreeNode* root, int path) {
        if (!root) return;

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




