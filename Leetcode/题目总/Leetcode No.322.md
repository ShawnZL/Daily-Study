# Leetcode No.322

###### [322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)

方法一：动态规划

边界条件：dp\[0] = 0, dp[i] = Max

状态转换方程：
$$
dp[i] = min(dp[i], dp[i - coins[j]] + 1)
$$

```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        if (amount == 0) return 0;
        int n = coins.size(), Max = amount + 1;
        vector<int> dp(amount + 1, Max);
        //初始化
        dp[0] = 0;
        for (int i = 1; i <= amount; i++) {
            for (int j = 0; j < n; j++) {
                if (i >= coins[j]) {
                    dp[i] = min(dp[i], dp[i - coins[j]] + 1);
                }
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
    }
};
```

