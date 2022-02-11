# Leetcode No.714

相较于[No.122](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/) 本题目就是多了一个限制条件fee，我们在卖出股票的同时将这个费用减去就好

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        vector<vector<int>> dp(n, vector<int>(2));
        dp[0][0] = 0; dp[0][1] = -prices[0];
        for (int i = 1; i < n; i++) {
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i] - fee);
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
        }
        return dp[n - 1][0];
    }
};
```

空间优化算法

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        int sell = 0, buy = -prices[0];
        for (int i = 1; i < n; ++i) {
            tie(sell, buy) = pair(max(sell, buy + prices[i] - fee), max(buy, sell - prices[i]));
        }
        return sell;
    }
};
```

**贪心算法，我们将手续费放在卖出时进行计算**

我们用 buy 表示在最大化收益的前提下，**如果我们手上拥有一支股票，那么它的最低买入价格是多少。**在初始时，buy 的值为prices[0] 加上手续费 fee。那么当我们遍历到第i (i>0) 天时：

如果prices[i] + fee < buy 我们跟新buy = prices[i] + fee

如果prices[i] > buy 我们可以获得收益为prices[i] - buy,但是此时操作不一定最优，因此我们提供一个反悔操作，看成当前当前手上拥有一支买入价格为prices[i] 的股票，将 buy 更新为 prices[i]。这样一来，如果下一天股票价格继续上升，我们会获得prices[i+1]−prices[i] 的收益，加上这一天 prices[i]−buy 的收益，恰好就等于在这一天不进行任何操作，而在下一天卖出股票的收益；

对于其余的情况，prices[i] 落在区间\[buy−fee,buy] 内，它的价格没有低到我们放弃手上的股票去选择它，也没有高到我们可以通过卖出获得收益，因此我们不进行任何操作。

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        int buy = prices[0] + fee;
        int profit = 0;
        for (int i = 1; i < n; i++) {
            if (prices[i] + fee < buy) {
                buy = prices[i] + fee;
            }
            else if (prices[i] > buy) {
                profit += prices[i] - buy;
                buy = prices[i];
            }
        }
        return profit;
    }
};
```

