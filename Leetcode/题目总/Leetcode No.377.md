# Leetcode No.377

###### [377. 组合总和 Ⅳ](https://leetcode-cn.com/problems/combination-sum-iv/)

使用动态规划：边际条件为：dp[0] = 1,

状态转移方程为
$$
dp[i] += dp[i - nums[j]]
$$
自己的代码没有通过**[10,20,30,40,50,60,70,80,90,100,110,120,130,140,150,160,170,180,190,200,210,220,230,240,250,260,270,280,290,300,310,320,330,340,350,360,370,380,390,400,410,420,430,440,450,460,470,480,490,500,510,520,530,540,550,560,570,580,590,600,610,620,630,640,650,660,670,680,690,700,710,720,730,740,750,760,770,780,790,800,810,820,830,840,850,860,870,880,890,900,910,920,930,940,950,960,970,980,990,111] 999** 

所以将 dp 数组设置为long long，但是依旧无法通过！

```c++
class Solution {
public:
    int combinationSum4(vector<int>& nums, int target) {
        int len = nums.size();
        vector<int> dp(target + 1);
        dp[0] = 1;
        for (int i = 1; i <= target; i++) {
            for (int j = 0; j < len; j ++) {
                if (i >= nums[j]) {
                    dp[i] += dp[i - nums[j]];
                }
            }
        }
        return dp[target];
    }
};
```

改进版本：多加限定条件 **dp[i - num] < INT_MAX - dp[i]**

```c++
class Solution {
public:
    int combinationSum4(vector<int>& nums, int target) {
        vector<int> dp(target + 1);
        dp[0] = 1;
        for (int i = 1; i <= target; i++) {
            for (int &num : nums) {
                if (i >= num && dp[i - num] < INT_MAX - dp[i]) {
                    dp[i] += dp[i - num];
                }
            }
        }
        return dp[target];
    }
};
```

本题目可以与[518. 零钱兑换 II](https://leetcode-cn.com/problems/coin-change-2/)可以做一个比较

1. 518根本区别是在于不在乎顺序，只看组合的总个数。但是本题目关注顺序问题。

 1 2 1 和 1 1 2 在518是一种情况，但是在377就不是一种情况。

2. 518 是先循环coins，但377coins循环在内部。

```c++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        int n = coins.size();
        
        vector<int> dp(amount + 1);
        dp[0] = 1;
        for (int &coin : coins) {
            for (int j = coin; j <= amount; j++) {
                dp[j] += dp[j - coin];
            }
        }
        return dp[amount];
    }
};
```

