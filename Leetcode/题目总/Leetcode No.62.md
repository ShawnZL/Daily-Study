# Leetcode No.62

###### [62. 不同路径](https://leetcode-cn.com/problems/unique-paths/)

就是使用简单的动态规划；边界条件是dp\[0][0] = 1，表示在原点就一个路径，状态转移方程为
$$
dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
$$
注意一点就是不要越界

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<vector<int>> dp(m, vector<int>(n));
        dp[0][0] = 1;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 && j != 0) {
                    dp[i][j] = dp[i][j - 1];
                }
                else if (i != 0 && j == 0) {
                    dp[i][j] = dp[i - 1][j];
                }
                else if (i != 0 && j != 0) {
                    dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
                }
            }
        }
        return dp[m - 1][n - 1];
    }
};
```

