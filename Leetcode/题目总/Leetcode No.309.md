# Leetcode No.309

###### [309. 最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

本题目多出的一个限制条件就是在购买一天后有一个冷却期，相较于No.122本题目就是在购买股票的方面，我们选择前两天的dp\[i-2][0]来购买股票

```
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if (n == 1) return 0;
        vector<vector<int>> dp(n, vector<int>(2));
        dp[0][0] = 0; dp[0][1] = -prices[0];
        dp[1][0] = max(dp[0][0], dp[0][1] + prices[1]);
        dp[1][1] = max(dp[0][1], dp[0][0] - prices[1]);
        //dp[i][0]表示当前不持有股票
        //dp[i][1]表示当前持有股票
        for (int i = 2; i < n; i++) {
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i]);
            dp[i][1] = max(dp[i - 1][1], dp[i - 2][0] - prices[i]);
            //买入股票应该算前两天
        }
        return dp[n - 1][0];
    }
};
```

方法二：

dp[i]表示当天结束时处于的状态。

dp\[i][0] 我们当前持有的股票可以是我们在i 天之前任何一天购买的，或者是没处于冷冻期的i天购买，那么要求i-1天必须不是冷冻期。
$$
dp[i][0]=max(dp[i - 1][0], dp[i - 1][2] - prices[i])
$$
dp\[i][1] 我们当前处于冷冻期，那么就是i天卖出了股票
$$
dp[i][1] = dp[i - 1][0] + prices[i]
$$
dp\[i][2] i天结束没有任何股票且不出冷冻期，说明当天无操作，就是i-1天我们没有股票；或者我们在i-1天处于冷冻期，
$$
dp[i][2] = max(dp[i - 1][2], dp[i - 1][1])
$$


```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if (n == 1) return 0;
        vector<vector<int>> dp(n, vector<int>(3));
        //dp[i][0]当前持有一支股票
        //dp[i][1]当前不持有任何股票，且处于冷冻期
        //dp[i][2]当前不持有任何股票，且不处于冷冻期
        dp[0][0] = -prices[0]; dp[0][1] = 0; dp[0][2] = 0;
        //dp[i][0]表示当前不持有股票
        //dp[i][1]表示当前持有股票
        for (int i = 1; i < n; i++) {
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][2] - prices[i]);
            //只有不处于冷冻期才才可以买
            dp[i][1] = dp[i - 1][0] + prices[i];
            //当天卖出了股票，处于冷冻期，无法购买
            dp[i][2] = max(dp[i - 1][2], dp[i - 1][1]);
            //买入股票应该算前两天
        }
        return max(dp[n - 1][1], dp[n - 1][2]);
    }
};
```



空间优化算法

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if (prices.empty()) {
            return 0;
        }

        int n = prices.size();
        int f0 = -prices[0];
        int f1 = 0;
        int f2 = 0;
        for (int i = 1; i < n; ++i) {
            int newf0 = max(f0, f2 - prices[i]);
            int newf1 = f0 + prices[i];
            int newf2 = max(f1, f2);
            f0 = newf0;
            f1 = newf1;
            f2 = newf2;
        }

        return max(f1, f2);
    }
};
```

