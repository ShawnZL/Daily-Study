# Leetcode No.981

###### [918. 环形子数组的最大和](https://leetcode-cn.com/problems/maximum-sum-circular-subarray/)

kadane算法就是一个简单的动态规划条件转移的特殊形式
$$
dp[i] = max(dp[i - 1], 0) + nums[i];
$$
无用的话就保存0。

kadane算法非常简单，很好理解。在这个题的条件下最大值结果无非是以下两种情况的一种： 最大值max在原数组中。 最大值max在环形数组中。 第一种情况无非就是Kadane算法。 我们思考第二种情况。假设输入数组为A，那么头尾元素A[0]和A[A.length-1]在第二种情况下一定会被选中，最大值有可能是去掉中间一段负数，那么就可以转换为计算非环形数组的最小值。

最大值最小值放置于一个循环完成

```c++
class Solution {
public:
    //动态规划
    //dp[i]表示以nums[i]结尾的最大和
    //转移方程为 dp[i] = max(dp[i-1] + nums[i], nums[i])
    //边界条件为 dp[0] = nums[0];
    //但是多了一个点就是环形，所以我们尝试1，设置一个当前节点的最大和的起始位置的数字start[i]

    int maxSubarraySumCircular(vector<int>& nums) {
        int len = nums.size(), maxn = nums[0], minn = nums[0], sum = nums[0];
        if (len == 1) return nums[0];
        vector<int> dp(len);
        vector<int> dpmin(len);
        dp[0] = nums[0];
        dpmin[0] = nums[0];
        for (int i = 1; i < len; i++) {
            sum += nums[i];//计算和
            //计算没有环形最大值
            dp[i] = max(dp[i - 1] + nums[i], nums[i]);
            //////////////
            if (i < len - 1) {//环形不计算第一个和最后一个数字，因为环形必定包含俩数字
                dpmin[i] = min(dpmin[i - 1] + nums[i], nums[i]);
            }
            maxn = max(dp[i], maxn);
            minn = min(dpmin[i], minn);
        }
        return maxn > sum - minn ? maxn : sum - minn;
        //得到没有环的最大值
    }


};
```

