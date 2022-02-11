# Leetcode No.121

#### [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

动态规划dp[i]记录当前节点之前最低价

```
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size(), ans = 0;
        vector<int> dp(n);
        //表示在第i天之前股票买入的最低价；
        dp[0] = prices[0];
        for (int i = 1; i < n; i++) {
            dp[i] = min(dp[i-1], prices[i]);
            ans = max(ans, prices[i] - dp[i]);
            //printf("%d:%d %d\n", i, dp[i], ans);
        }
        return ans;
    }
};
```

空间优化：

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int inf=1e9;
        int minprice=inf,maxprofit=0;//最小值，最大值
        for(int price:prices){//price表示其中的一个数字
            maxprofit=max(maxprofit,price-minprice);
            minprice=min(price,minprice);
        }
        return maxprofit;
    }
};
```

