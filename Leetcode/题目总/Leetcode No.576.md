# Leetcode No.576

###### [576. 出界的路径数](https://leetcode-cn.com/problems/out-of-boundary-paths/)

我的方法：递归调用，但是面对[8, 7, 16, 1, 5]出现了超过时间限制的

```c++
class Solution {
public:
    int ans = 0;
    void could(int m1, int n1, int maxMove1, int row1, int col1) {
        //步数是否没有了
        if (maxMove1 < 0) return;
        //判断是否出界
        if (row1 < 0 || row1 >= m1 || col1 < 0 || col1 >= n1) {
            ans++;
            return;
        }
        //判断是否到达边界
        /*if ((row1 == 0 || row1 == (m1 - 1) || col1 == 0 || col1 == (n1 - 1)) && maxMove1 >= 1) {
            ans++;
        }*/
        could(m1, n1, maxMove1 - 1, row1 + 1, col1);
        could(m1, n1, maxMove1 - 1, row1 - 1, col1);
        could(m1, n1, maxMove1 - 1, row1, col1 + 1);
        could(m1, n1, maxMove1 - 1, row1, col1 - 1);
    }
    int findPaths(int m, int n, int maxMove, int startRow, int startColumn) {
        //vector<vector<int>> dp(m, vector<int>(n));
        could(m, n, maxMove, startRow, startColumn);
        return ans;
    }
};
```

dfs

```c++
class Solution 
{
public:
    const int MOD = (int)1e9 + 7;
    const int dirs[4][2] = {{-1,0}, {1,0}, {0,-1}, {0,1}};

    int Row;
    int Col;
    int maxMove;
    int visited [50][50][50];

    int findPaths(int m, int n, int maxMove, int startRow, int startColumn) 
    {
        Row = m;
        Col = n;
        this->maxMove = maxMove;

        memset(visited, -1, sizeof(visited));
        
        return dfs(startRow, startColumn, 0);
    }

    int dfs(int r, int c, int step)
    {
        if (!(0 <= r && r < Row) || !(0 <= c && c < Col))
            return 1;
        if (step == maxMove)
            return 0;
        if (visited[r][c][step] != -1)
            return visited[r][c][step];

        int res = 0;
        for (int di = 0; di < 4; di ++)
        {
            int dr = dirs[di][0],  dc = dirs[di][1];
            int nr = r + dr,  nc = c + dc;
            res += dfs(nr, nc, step + 1);
            res %= MOD;
        }
        visited[r][c][step] = res;
        return res;
    }
};

```





方法三：动态规划

dp\[i]\[j][k]表示球移动i次之后位于[j, k]的数目， i = 0一定位于起始坐标处，dp[0]\[startRow][startColumn] = 1 其他dp[0] = 0， 在球移动了i次以后位于(j , k) , i + 1位于[j\`, k`]处

当 0 <= j\` <m 且 0<= k\` < n, 球在i + 1之后没有出界，dp\[i + 1]\[j\`][k`] += dp\[i]\[j][k];

否则出界，就将dp\[i]\[j][k] 的数值加到出界的路径处

```c++
class Solution {
public:
    static constexpr int MOD = 1'000'000'007;

    int findPaths(int m, int n, int maxMove, int startRow, int startColumn) {
        vector<vector<int>> dirsctions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        int outCount = 0;
        vector<vector<vector<int>>> dp(maxMove + 1, vector<vector<int>>(m, vector<int>(n)));
        dp[0][startRow][startColumn] = 1;
        for (int i = 0; i < maxMove; i++) {
            for (int j = 0; j < m; j++) {
                for (int k = 0; k < n; k++) {
                    int count = dp[i][j][k];
                    if (count > 0) {
                        for (auto &dirsction : dirsctions) {
                            int j1 = j + dirsction[0];
                            int k1 = k + dirsction[1];
                            if (j1 >= 0 && j1 < m && k1 >= 0 && k1 < n) {
                                dp[i + 1][j1][k1] = (dp[i + 1][j1][k1] + count) % MOD;
                            }
                            else {
                                outCount = (outCount + count) % MOD;
                            }
                        }
                    }
                }
            }
        }
        return outCount;
    }
};
```



空间优化，因为dp[i]\[]\[] 只有在dp[i + 1]\[]\[] 才可以使用的上

```c++
class Solution {
public:
    static constexpr int MOD = 1'000'000'007;

    int findPaths(int m, int n, int maxMove, int startRow, int startColumn) {
        vector<vector<int>> directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        int outCounts = 0;
        vector<vector<int>> dp(m, vector<int>(n));
        dp[startRow][startColumn] = 1;
        for (int i = 0; i < maxMove; i++) {
            vector<vector<int>> dpNew(m, vector<int>(n));
            for (int j = 0; j < m; j++) {
                for (int k = 0; k < n; k++) {
                    int count = dp[j][k];
                    if (count > 0) {
                        for (auto& direction : directions) {
                            int j1 = j + direction[0], k1 = k + direction[1];
                            if (j1 >= 0 && j1 < m && k1 >= 0 && k1 < n) {
                                dpNew[j1][k1] = (dpNew[j1][k1] + count) % MOD;
                            } else {
                                outCounts = (outCounts + count) % MOD;
                            }
                        }
                    }
                }
            }
            dp = dpNew;
        }
        return outCounts;
    }
};

```

