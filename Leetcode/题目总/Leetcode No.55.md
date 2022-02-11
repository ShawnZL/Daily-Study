# Leetcode No.55

[No.55跳跃游戏](https://leetcode-cn.com/problems/jump-game/submissions/)

方法一：模拟，挨个跳

方法二：right,记录可以跳到的最右端距离

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int len = nums.size();
        vector<int> dp(len+10, 0);
        dp[0] = 1;
        for (int i = 0; i < len; i++) {
            if (dp[i] == 0) break;
            if (dp[len-1] == 1) break;
            if (nums[i] >= (len - i - 1)) {
                dp[len-1] = 1;
                break;
            }
            for (int j = 1; j <= nums[i]; j++) {
                dp[j+i] = 1;
                //printf("%d=%d ", j+i, dp[j+i]); 
            }
            //printf("%d ", dp[i]);
        }
        if (dp[len-1] == 0) return false;
        return true;
    }
};
```



```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int right = 0, len = nums.size();
        for (int i = 0; i < len; i++) {
            if (i <= right) {
                right = max(right, nums[i] + i);
                if (right >= len - 1) return true;
            }
        }
        return false;
    }
};
```

