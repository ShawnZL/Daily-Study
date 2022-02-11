# Leetcode No.45

[No.45跳跃游戏II](https://leetcode-cn.com/problems/jump-game-ii/submissions/)

```
class Solution {
public:
    int jump(vector<int>& nums) {
        int len = nums.size();
        if (len == 1) return 0;
        vector<int> dp(len+10000, 0);
        //dp保存到当前节点的最小跳数
        //dp[0] = 0;
        for (int i = 0; i < len; i++) {
            if (dp[len-1]) return dp[len-1];
            for (int j = 1; j <= nums[i]; j++) {
                if (dp[i+j] == 0) 
                    dp[i+j] = dp[i] + 1;
                else
                    dp[i+j] = min(dp[i+j], dp[i] + 1);
                //printf("%d %d\n", i+j, dp[i+j]);
            }
        }
        return dp[len-1];
    }
};
```



在具体的实现中，我们维护当前能够到达的最大下标位置，记为边界。我们从左到右遍历数组，到达边界时，更新边界并将跳跃次数增加1。在遍历数组时，我们不访问最后一个元素，这是因为在访问最后一个元素之前，我们的边界一定大于等于最后一个位置，否则就无法跳到最后一个位置了。如果访问最后一个元素，在边界正好为最后一个位置的情况下，我们会增加一次「不必要的跳跃次数」，因此我们不必访问最后一个元素。

```c++
class Solution {
public:
    int jump(vector<int>& nums) {
        int maxpos = 0, n = nums.size(), end = 0, step = 0;
        for (int i = 0; i < n - 1; i++) {
            if (maxpos >= i) {
                maxpos = max(maxpos, i + nums[i]);
                //更新位置
                if (i == end) {
                    end = maxpos;
                    ++step;
                }
            }
        }
        return step;
    }
};
```

