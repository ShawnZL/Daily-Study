# Leetcode No.279

###### [279. 完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

方法一：动态规划

dp[i] 表示当前 i 最少有几个完全平方数相加为 i，nums为记录当前的完全平方数。状态转移方程为下所示
$$
dp[i]=min(dp[i],dp[i - nums[j]] + 1)
$$
注意事项：在书写for循环判断出界条件为 nums[j] <= i && j < nums.size()。这里是两个条件必须都要满足，当写成 j < nums.size() && nums[j] <= i 时不会发生溢出，**因为要判断是否越界，如果越界其它都白搭。**

```c++
class Solution {
public:
    int numSquares(int n) {
        vector<int> dp(n + 1, n + 1);
        dp[0] = 0; dp[1] = 1;
        vector<int> nums;
        for (int i = 1; i <= n; i++) {
            if (i * i <= n) {
                nums.push_back(i * i);
            }
             for (int j = 0; j < nums.size() && nums[j] <= i; j++) {
                dp[i] = min(dp[i], dp[i - nums[j]] + 1);
            }
            //溢出问题？？？
            /*for (int j = 0; nums[j] <= i && j < nums.size(); j++) {
                dp[i] = min(dp[i], dp[i - nums[j]] + 1);
            }*/
        }
        return dp[n];
    }
};
```

简易版

```c++
class Solution {
public:
    int numSquares(int n) {
        vector<int> dp(n + 1);
        for (int i = 1; i <= n; i++) {
            int minn = INT_MAX;
            for (int j = 1; j * j <= i; j++) {
                minn = min(minn, dp[i - j * j]);
            }
            dp[i] = minn + 1;
        }
        return dp[n];
    }
};
```

