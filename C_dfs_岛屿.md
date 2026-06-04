


### 1. [200] 岛屿数量

进入dfs条件: grid[x][y] == 1

递归终止条件: 

1. x,y越界
2. grid[x][y] == 0

执行动作: 岛屿 --> 海洋


```c++
class Solution {
public:
    int m;
    int n;
    void dfs(vector<vector<char>>& grid, int x, int y) {
        // 越界
        if (x < 0 || x >= m || y < 0 || y >= n) return;

        // 当前节点不是岛屿
        if (grid[x][y] != '1') return;

        grid[x][y] = '0'; // 岛屿 --> 海洋

        dfs(grid, x+1, y);
        dfs(grid, x-1, y);
        dfs(grid, x, y+1);
        dfs(grid, x, y-1);
    }

    int numIslands(vector<vector<char>>& grid) {
        int ans = 0;

        m = grid.size();
        n = grid[0].size();
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (grid[i][j]=='1') { // 是岛屿
                    dfs(grid, i, j);
                    ans++;
                }
            }
        }

        return ans;
    }
};
```


### 2. [695] 岛屿的最大面积

```c++
class Solution {
public:
    int m;
    int n;

    int dfs(vector<vector<int>>& grid, int x, int y) {
        // 越界
        if (x<0 || x>=m || y<0 || y>=n) return 0;

        if (grid[x][y] != 1) return 0; // 不是岛屿

        grid[x][y] = 0; // 岛屿 --> 海洋

        return 1 +
            dfs(grid, x+1, y) +
            dfs(grid, x-1, y) +
            dfs(grid, x, y+1) +
            dfs(grid, x, y-1);
    }

    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int ans = 0;

        m = grid.size();
        n = grid[0].size();

        for (int i=0; i<m; i++) {
            for(int j=0; j<n; j++) {
                if (grid[i][j] == 1) {
                    ans = max(ans, dfs(grid, i, j));
                }
            }
        }

        return ans;
    }
};
```

### 3. [463] 岛屿的周长 （Easy）

统计多个岛屿各自的周长

注意: 为了防止陆地格子在深度优先搜索中被重复遍历导致死循环，需要将遍历过的陆地格子标记为已经遍历过，下面的代码中我们设定值为 2 的格子为已经遍历过的陆地格子


- 当 grid[x][y] 的`越界` or `大海`时，说明碰到了岛屿的边界，return 1
- 当 grid[x][y] `是岛屿=1`时，将 grid[x][y]设置为2，涂上颜色(表示已经访问过)，继续递归
- 当 grid[x][y] `已经被访问过(=2)`时，不在重复访问，返回0

```c++
class Solution {
public:
    int m;
    int n;
    int dfs(vector<vector<int>>& grid, int x, int y) {

        { // 遇到了边 --> 周长+1
            if (x<0 || x>=m || y<0 || y>=n) return 1; // 越界
            if (grid[x][y] == 0) return 1; // 0 不是岛屿
        }

        // 2 岛屿已涂上颜色 --> 不重复累加
        if (grid[x][y] == 2) return 0;

        grid[x][y] = 2; // 岛屿 --> 涂上颜色

        return
            dfs(grid, x+1, y) +
            dfs(grid, x-1, y) +
            dfs(grid, x, y+1) +
            dfs(grid, x, y-1);
    }

    int islandPerimeter(vector<vector<int>>& grid) {
        int ans = 0;

        m = grid.size();
        n = grid[0].size();
        for (int i=0; i<m; i++) {
            for (int j=0; j<n; j++) {
                if (grid[i][j]==1) { // 是岛屿
                    ans += dfs(grid, i, j);
                }
            }
        }

        return ans;
        
    }
};
```

### 4. 😭 [827] 最大人工岛 （Hard）

给你一个大小为 n x n 二进制矩阵 grid 。最多 只能将一格 0 变成 1 。 返回执行此操作后，grid 中最大的岛屿面积是多少？



