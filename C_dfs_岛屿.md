


### 1. [200] 岛屿数量

进入dfs条件: grid[x][y] == 1

递归终止条件: 

1. x,y越界
2. grid[x][y] == 0 // 大海
3. grid[x][y] == 2 // 岛屿（已遍历过）

执行动作: 岛屿 --> 海洋


```c++
class Solution {
public:
    int m;
    int n;
    void dfs(vector<vector<char>>& grid, int x, int y) {
        // 越界
        if (x<0 || x>=m || y<0 || y>=n) return;

        switch (grid[x][y]) {
            case '0': // 大海
                return;
            case '1': // 岛屿(未遍历过)
                grid[x][y] = '2'; // 标记为(已遍历过) --> 防止递归死循环
                dfs(grid, x+1, y);
                dfs(grid, x-1, y);
                dfs(grid, x, y+1);
                dfs(grid, x, y-1);
                return;
            case '2': // 岛屿(已遍历过)
                return;
        }
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
        switch (grid[x][y]) {
        case 0: // 大海
            return 0;
        case 1: // 岛屿(未遍历过)
            grid[x][y] = 2; // 标记为(已遍历过) --> 防止递归死循环
            return 1 +
                dfs(grid, x+1, y) +
                dfs(grid, x-1, y) +
                dfs(grid, x, y+1) +
                dfs(grid, x, y-1);
        case 2: // 岛屿(已遍历过)
            return 0;
        }
        return 0;
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

- 越界
- 大海

```c++
class Solution {
public:
    int m;
    int n;
    int dfs(vector<vector<int>>& grid, int x, int y) {

        if (x<0 || x>=m || y<0 || y>=n) return 1; // 越界 --> 碰到周围的线

        switch (grid[x][y]) {
        case 0: // 大海 --> 碰到周围的线
            return 1;
        case 1: // 岛屿(未遍历过)
            grid[x][y] = 2;
            return
                dfs(grid, x+1, y) +
                dfs(grid, x-1, y) +
                dfs(grid, x, y+1) +
                dfs(grid, x, y-1);
        case 2: // 岛屿(已遍历过)
            return 0;
        }
        return 0;
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



