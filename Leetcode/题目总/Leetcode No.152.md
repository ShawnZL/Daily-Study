# Leetcode No.152

#### [152. 乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)

本题目容易忽视一个情况，对于[1,2,-3,-4],如果单一使用dp[i]记录结果，只会记录到2，但是事实最大结果却是12，所以我动态维护一个dpminius，保存最小值，此时条件转移方程为：
$$
fmax[i] = max(fmax[i-1] * nums[i], max(nums[i], fmin[i-1] * nums[i]))
$$

$$
fmin[i] = min(fmin[i-1] * nums[i], min(nums[i], fmax[i-1] * nums[i]))
$$



```c++
class Solution {
public:
    int maxProduct(vector<int>& nums) {
    //dp[i]表示因i结尾的最大乘积
        int len = nums.size(), ans = nums[0];
        vector<int> dp(len);
        vector<int> dpminus(len);
        dp[0] = nums[0];
        dpminus[0] = nums[0];
        for (int i = 1; i < len; i++) {
            dp[i] = max(dpminus[i - 1] * nums[i], max(nums[i], dp[i-1] * nums[i]));
            dpminus[i] = min(dpminus[i - 1] * nums[i], min(nums[i], dp[i - 1] * nums[i]));
            ans = max(ans, dp[i]);
        }
        return ans;
    }
};
```

空间优化

```c++
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int maxF = nums[0], minF = nums[0], ans = nums[0];
        for (int i = 1; i < nums.size(); ++i) {
            int mx = maxF, mn = minF;
            maxF = max(mx * nums[i], max(nums[i], mn * nums[i]));
            minF = min(mn * nums[i], min(nums[i], mx * nums[i]));
            ans = max(maxF, ans);
        }
        return ans;
    }
};

```

