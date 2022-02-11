# Leetcode No.70

[爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)

本体就是动态规划的简单应用，注意一点就是数组初始化时应该是一个具体的数，而不是未赋值参数

**例如int dp[n] 就不是正确的。**

```c++
class Solution {
public:
    int climbStairs(int n) {
        int dp[10000];
        dp[1] = 1;
        dp[2] = 2;
        for (int i = 3; i <= n; i++) {
            dp[i] = dp[i-1] + dp[i-2];//状态转换方程
        }
        return dp[n];
    }
};
```

