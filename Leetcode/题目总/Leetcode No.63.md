# Leetcode No.63

###### [63. 不同路径 II](https://leetcode-cn.com/problems/unique-paths-ii/)

动态规划算法，首先判定该点是否为障碍点，如果为障碍点，那么没有任何点可以到达，所以到达路径为0，就设置该点dp\[i][j] = 0，状态转移方程还是写成
$$
dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
$$

```c++
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        vector<vector<int>> dp(m, vector<int>(n));
        if (obstacleGrid[0][0] == 1)
            dp[0][0] = 0;
        else 
            dp[0][0] = 1;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (obstacleGrid[i][j] != 1) {
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
                else {
                    obstacleGrid[i][j] = 0;
                }
            }
        }
        return dp[m - 1][n - 1];
    }
};
```

