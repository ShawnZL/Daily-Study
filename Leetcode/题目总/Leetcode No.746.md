# Leetcode No.746

[746.使用最小花费爬楼梯](https://leetcode-cn.com/problems/min-cost-climbing-stairs/submissions/)

本题写地过程中，**将状态转移方程写成了dp[i] = min(cost[i-1], cost[i-2]) + cost[i]。**这个是对于状态转移方程掌握不完全，因为我们转移的不是花费，而是每一个的状态，状态就是**dp[i]。**

```c++
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        int dp[1010];//记录cost
        int len = cost.size();
        dp[0] = cost[0];
        dp[1] = cost[1];//无论如何，走到这一步都是直接更省，少了cost[0]的花费
        for (int i = 2; i <len; i++) {
            dp[i] = min(dp[i-1], dp[i-2]) + cost[i];//状态转移方程
            //printf("%d=%d, %d %d %d\n", i, dp[i], cost[i-2], cost[i-1], cost[i]);
        }
        //最后一阶台阶为len,而不是len-1
        return min(dp[len-1], dp[len-2]);
    }
};
```

