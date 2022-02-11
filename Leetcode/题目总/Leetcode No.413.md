# Leetcode No.413

###### [413. 等差数列划分](https://leetcode-cn.com/problems/arithmetic-slices/)

利用动态规划，dp数组表示以 nums[i] 结尾的当差数列长度， 初始条件设置为dp[0] = 1; dp[1] = 2;

状态转移方程满足
$$
(nums[i] - nums[i - 1]) == (nums[i - 1] - nums[i - 2])
$$
dp[i] = dp[i - 1] + 1; 否则设置dp[i] = 2; 当dp[i] < dp[i - 1] 时，表示一个当差数列的结束，记录当前长度可以有多少等差数列

```c++
class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& nums) {
        int ans = 0, temp = 0,n = nums.size();
        if (n < 3) return 0;
        vector<int> dp(n);
        dp[0] = 1; dp[1] = 2;
        for (int i = 2; i < n; i++) {
            if ((nums[i] - nums[i - 1]) == (nums[i - 1] - nums[i - 2])) {
                dp[i] = dp[i - 1] + 1;
            }
            else {
                dp[i] = 2;
            }
            
            if (dp[i] >= dp[i - 1]) {
                temp = max(temp, dp[i]);
            }
            else {
                //printf("%d %d\n", i,temp);
                ans += (temp - 2 + 1) * (temp - 2) / 2;
                temp = 0;
            }
        }
        //printf("%d %d", temp, ans);
        if (temp >= 3)
            ans += (temp - 2 + 1) * (temp - 2) / 2;
        return ans;
    }
};
```

官方做法

```c++
class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& nums) {
        int n = nums.size();
        if (n == 1) {
            return 0;
        }

        int d = nums[0] - nums[1], t = 0;
        int ans = 0;
        // 因为等差数列的长度至少为 3，所以可以从 i=2 开始枚举
        for (int i = 2; i < n; ++i) {
            if (nums[i - 1] - nums[i] == d) {
                ++t;
            }
            else {
                d = nums[i - 1] - nums[i];
                t = 0;
            }
            //太骚了这一步
            ans += t;
        }
        return ans;
    }
};
```

