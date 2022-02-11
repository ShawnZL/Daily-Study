# Leetcode No.343

###### [343. 整数拆分](https://leetcode-cn.com/problems/integer-break/)

方法一：动态规划

自己思考，dp[i] 记录当前 i 拆分后的最大和，类似于贪心，局部最优达到全局最优。初始条件dp[0] = 0；dp[1] = 0(1 * 0)；dp[2] = 1 (1 * 1)

条件转移方程为
$$
dp[i] = max(d[i], dp[j] * (i - j))
$$

```c++
class Solution {
public:
    int integerBreak(int n) {
        vector<int> dp(n + 1);
        dp[0] = 0; dp[1] = 0; dp[2] = 1; dp[3] = 2; dp[4] = 4;
        for (int i = 5; i <= n; i++) {
            for (int j = 4; j < i; j++) {
                dp[i] = max(dp[i], dp[j] * (i - j));
            }
        }
        return dp[n];
    }
};
```

正确情况的状态转移方程为
$$
dp[i]=max{max(j*(i-j),j*dp[i-j])}
$$

```c++
class Solution {
public:
    int integerBreak(int n) {
        vector<int> dp(n + 1);
        for (int i = 2; i <= n; i++) {
            int curMax = 0;
            for (int j = 1; j < i; j++) {
                curMax = max(curMax, max(j * (i - j), j * dp[i - j]));
            }
            dp[i] = curMax;
        }
        return dp[n];
    }
};
```

