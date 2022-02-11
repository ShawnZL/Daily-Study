# Leetcode No.213

[打家劫舍II](https://leetcode-cn.com/problems/house-robber-ii/)

[友情链接打家劫舍I](https://leetcode-cn.com/problems/house-robber/)

本题目的难点在于第一与最后一个不能同时偷，这里我们分开考虑，只有第一家[0, len - 2], 和只偷最后一家[1, len - 1]

利用函数的参数start, end分别表示起始位置，结尾位置

那么边界条件重写为
$$
dp[start] = nums[start]
dp[start + 1] = max (nums[start], nums[start + 1]);
$$


```c++
class Solution {
public:
    int robb(vector<int>& nums, int start, int end) {
        int first = nums[start], second = max(nums[start], nums[start+1]);
        for (int i = start + 2; i <= end; i++) {
            int temp = second;
            second = max(first + nums[i], second);
            first = temp;
        }
        return second;
    }
    int rob(vector<int>& nums) {
        //边界条件dp[0] = nums[0], dp[1] = max(nums[0], nums[1]);
        //转移条件dp[i] = max(dp[i-2] + nums[i], dp[i-1]);
        //分别求出不选择最后一个的最大值和不选择第一个的最大值
        //[0, len-2] [1, len-1]
        if (nums.empty()) return 0;
        int len = nums.size();
        if (len == 1)
            return nums[0];
        else if (len == 2) 
            return max(nums[0], nums[1]);
        return max(robb(nums, 0, len-2), robb(nums, 1, len-1));
    }
};
```

