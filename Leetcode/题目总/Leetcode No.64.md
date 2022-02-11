# Leetcode No.64

###### [64. 最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)

典型动态规划，边界条件：dp\[0][0] = grid\[0][0]，状态转移方程
$$
dp[i][j] = min(dp[i - 1][j], dp[i][j - 1])
$$


```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> dp(m, vector<int>(n));
        //dp[0][0] = grid[0][0];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 && j != 0) {
                    dp[i][j] = dp[i][j - 1];
                }
                else if (i != 0 && j == 0) {
                    dp[i][j] = dp[i - 1][j];
                }
                else if (i != 0 && j != 0) {
                    dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]);
                }
                dp[i][j] += grid[i][j];
            }
        }
        return dp[m - 1][n - 1];
    }
};
```

