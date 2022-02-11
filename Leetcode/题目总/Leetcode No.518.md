# Leetcode No.518

###### [518. 零钱兑换 II](https://leetcode-cn.com/problems/coin-change-2/)

联系 [322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)

方法一：动态规划，但是多考虑了顺序问题，同时初始条件也给出有问题，应该为dp[0] = 1;

```c++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int n = coins.size(), Max = amount + 1;
        vector<int> dp(amount + 1);
        dp[0] = 0;

        /*for (int i = 0; i < n; i++) {
            dp[coins[i]] = 1;
        }*/
        //这个方法多考虑了一种情况就是3 1,1,1; 2,1; 1,2;
        //将顺序也考虑进去了
        for (int i = 1; i <=amount; i++) {
            for (int j = 0; j < n; j++) {
                if (i > coins[j]) {
                    dp[i] = dp[i] + dp[i - coins[j]];
                }
                if (i == coins[j]) {
                    dp[i] = dp[i] + dp[i - coins[j]] + 1;
                }
                printf("%d %d\n", i, dp[i]);
            }
        }
        return dp[amount];
    }
};
```

**边界条件 dp[0] = 1**

状态转移方程
$$
dp[i] += dp[i - coins[j]]
$$


```c++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int n = coins.size(), Max = amount + 1;
        vector<int> dp(amount + 1);
        dp[0] = 1;

        for (int& coin : coins) {
            for (int i = coin; i <= amount; i++) {
                dp[i] += dp[i - coin];
            }
        }
        return dp[amount];
    }
};
```

