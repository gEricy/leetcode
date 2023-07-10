

## [DFS](https://leetcode-cn.com/problems/number-of-islands/solution/dao-yu-lei-wen-ti-de-tong-yong-jie-fa-dfs-bian-li-/) 深度优先遍历

下面是最简单的二叉树的前序遍历递归写法：

```C++
void dfs(TreeNode root) {
    if (root == null) { // 判断 base case
        return;
    }
    
    cout << root.val << endl;  // 访问root
    
    // 访问两个相邻结点：左子结点、右子结点
    dfs(root.left);
    dfs(root.right);
}
```

----

### 1. [200. 岛屿数量](https://leetcode-cn.com/problems/number-of-islands/)

```c++
class Solution {
    int m, n;
public:
    // dfs结束后，会将为'1'的位置，改成'0
    void dfs(vector<vector<char>>& grid, int x, int y) {
        if (x < 0 || x >= m || y < 0 || y >= n)  // 不在岛屿内
            return;

        if (grid[x][y] != '1')  // 节点不是岛屿
            return;

        grid[x][y] = 0; // 将岛屿变成陆地

        dfs(grid, x, y+1);
        dfs(grid, x+1, y);
        dfs(grid, x, y-1);
        dfs(grid, x-1, y);
    }

    int numIslands(vector<vector<char>>& grid) {
        m = grid.size();
        n = grid[0].size();
        int cnt = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    dfs(grid, i, j);
                    cnt++;
                }
            }
        }

        return cnt;
    }
};
```

```python
class Solution(object):
    def numIslands(self, grid):
        m = len(grid)
        n = len(grid[0])

        def dfs(grid, i, j):
            if i < 0 or i >= m or j < 0 or j >= n:  # 越界
                return
            if grid[i][j] == "0":
                return
            grid[i][j] = "0"

            dfs(grid, i + 1, j)
            dfs(grid, i - 1, j)
            dfs(grid, i, j + 1)
            dfs(grid, i, j - 1)

        ans = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == "1":
                    dfs(grid, i, j)
                    ans += 1

        return ans
```

### 2. [695. 岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island/)

```c++
class Solution {
private:
    int m;
    int n;
public:
    // 深度优先遍历: 一直将岛屿变成陆地
    // 返回值: 包含(x,y)的岛屿的面积
    int dfs(vector<vector<int>>& grid, int x, int y) {
        if (x < 0 || x >= m || y < 0 || y >= n)  // 不在岛屿内
            return 0;

        if (grid[x][y] != 1)  // 节点不是岛屿
            return 0;

        grid[x][y] = 0; // 将岛屿变成陆地

        return 1 + dfs(grid, x, y+1)
                 + dfs(grid, x+1, y)
                 + dfs(grid, x, y-1)
                 + dfs(grid, x-1, y);
    }

    int maxAreaOfIsland(vector<vector<int>>& grid) {
        m = grid.size();
        n = grid[0].size();
        int ans = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                // if (grid[i][j] == 1) {
                    ans = max( ans, dfs(grid, i, j) );
                // }
            }
        }

        return ans;
    }
};
```

```python
class Solution(object):
    def maxAreaOfIsland(self, grid):
        m = len(grid)
        n = len(grid[0])

        def dfs(grid, i, j):
            if i < 0 or i >= m or j < 0 or j >= n:
                return 0
            if grid[i][j] == 0:
                return 0
            grid[i][j] = 0
            return (
                1
                + dfs(grid, i + 1, j)
                + dfs(grid, i - 1, j)
                + dfs(grid, i, j + 1)
                + dfs(grid, i, j - 1)
            )

        ans = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == 1:
                    ans = max(ans, dfs(grid, i, j))
        return ans
```
