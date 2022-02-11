# Leetcode No.198

[打家劫舍](https://leetcode-cn.com/problems/house-robber/submissions/)

我自己考虑的使用一维代码，但是没有考虑到[2, 1, 1, 2]这个情况使用我的代码只能得到3，并不是结果四，应该在每一个for循环下加入一个for，dp[i] 表示在打劫第i家时可以得到的最大值，

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
    //状态转移方程dp[i] = max(dp[i-1], dp[i-2] + nums[i])
        int len = nums.size();
        if (len == 1) return nums[0];//直接面对只有一家的情况
        int dp[110] = {0};
        dp[0] = nums[0];
        dp[1] = nums[1];
        if (len == 2) return max(dp[0], dp[1]);
        for (int i = 2; i < len; i++) {
            dp[i] = max(dp[i-1], dp[i-2] + nums[i]);
        }
        return dp[len-1];
    }
};
```

本人完成代码，同时选择一个maxn记录最大值。

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int len = nums.size(), maxn = 0;
        int dp[110] = {0};
        dp[1] = nums[0];
        if (len == 1) return dp[1];
        dp[2] = nums[1];
        if (len == 2) return max(dp[1], dp[2]);
        for (int i = 3; i <= len; i++) {
            for (int j = 1; j < i - 1; j++) {
                dp[i] = max(dp[j] + nums[i-1], dp[i]);
            }
            maxn = max(dp[i], dp[i-1]);
        }
        return maxn;
    }
};
```



官方总结使用dp[i]表示前i个房间可以偷窃的最大值
$$
dp[1] = max(dp[i-2] + nums[i], dp[i-1])
$$
边界条件为：
$$
dp[0] = nums[0];
dp[1] = max(dp[1], dp[0]);
$$

```c++
class Solution {
public:
    int rob(vector<int>& nums) {
        int len = nums.size();
        int dp[110];
        dp[0] = nums[0];
        if (len == 1) return dp[0];
        dp[1] = max(nums[0], nums[1]);//这个很关键
        for (int i = 2; i < len; i++) {
            dp[i] = max(dp[i-2] + nums[i], dp[i-1]);
            printf("%d %d\n", i, dp[i]);
        }
        return dp[len-1];
    }
};
```

