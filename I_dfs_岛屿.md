


### 1. [200] 岛屿数量

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


### 2. [695] 岛屿的最大面积

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
                ans = max( ans, dfs(grid, i, j) );
            }
        }

        return ans;
    }
};
```

### 3. [463] 岛屿的周长 （Easy）

统计多个岛屿各自的周长

注意: 为了防止陆地格子在深度优先搜索中被重复遍历导致死循环，需要将遍历过的陆地格子标记为已经遍历过，下面的代码中我们设定值为 2 的格子为已经遍历过的陆地格子


```c++
class Solution {
private:
    int m;
    int n;
public:
    int dfs(vector<vector<int>>& grid, int x, int y) {
        if (x < 0 || x >= m || y < 0 || y >= n)  // 不在岛屿内
            return 1;

        if (grid[x][y] == 0)  // 节点是大海
            return 1;

        if (grid[x][y] == 2) // 已经遍历过的陆地，不在重复执行了，直接返回0
            return 0;

        grid[x][y] = 2; // 遍历过的陆地，标记为2，表示已经遍历过

        return dfs(grid, x, y+1)
                 + dfs(grid, x+1, y)
                 + dfs(grid, x, y-1)
                 + dfs(grid, x-1, y);
    }

    int islandPerimeter(vector<vector<int>>& grid) {
        m = grid.size();
        n = grid[0].size();

        int ans = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
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



