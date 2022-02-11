# Leetcode No.122

#### [122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

动态规划

dp\[i][0] 表示第i天交易完成后手里没有股票的最大利润，dp\[i][1]表示第i天交易完成以后手里持有一只股票的最大利润
$$
dp[i][0]=max\{dp[i-1][0],dp[i-1][1]+prices[i]\}
$$

$$
dp[i][1]=max\{dp[i−1][1],dp[i−1][0]-prices[i]\}
$$

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<vector<int>> dp(n, vector<int>(2));
        dp[0][0] = 0, dp[0][1] = -prices[0];
        for (int i = 1; i < n; i++) {
            dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i]);
            dp[i][1] = max(dp[i-1][1], dp[i-1][0] - prices[i]);
        }
        return dp[n-1][0];
    }
};
```

空间优化：

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        //vector<vector<int>> dp(n, vector<int>(2));
        int dp0 = 0, dp1 = -prices[0];
        for (int i = 1; i < n; i++) {
            int temp0 = dp0, temp1 = dp1;
            dp0 = max(temp0, temp1 + prices[i]);
            dp1 = max(temp1, temp0 - prices[i]);
        }
        return dp0;
    }
};
```

方法二：贪心法：

[解释](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/solution/mai-mai-gu-piao-de-zui-jia-shi-ji-ii-by-leetcode-s/)

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {   
        int ans = 0;
        int n = prices.size();
        for (int i = 1; i < n; ++i) {
            ans += max(0, prices[i] - prices[i - 1]);
        }
        return ans;
    }
};
```

