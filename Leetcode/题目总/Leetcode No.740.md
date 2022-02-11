# Leetcode No.740

[删除并获得数](https://leetcode-cn.com/problems/delete-and-earn/submissions/)

动态规划问题 使用temp记录从小到大非重复数字，sum记录重复数字的和

状态转移方程
$$
temp[i] != temp[i-1] + 1;也就是后一个数字永远不与前一个数字相连；
dp[i] = dp[i-1] + sun[i];
$$

$$
temp[i] == temp[i-1]+1; dp[i] = max(sum[i], dp[i-1])
$$

边界条件
$$
dp[0] = sum[0];
dp[1] = max(sum[0], sum[1])此时为相邻情况；
dp[1] = sum[1] + dp[0]此时为不相邻；
$$
另外本体最应该重视越界情况

```c++
class Solution {
public:
    //本题思路先排序nums，将nums中不同的数字选择出来，
    int deleteAndEarn(vector<int>& nums) {
        if (nums.empty()) return 0;
        sort(nums.begin(), nums.end());
        int len = nums.size();
        if (len == 1) return nums[0];
        vector<int> temp;//记录没有重复的数据
        vector<int> sum;//记录数据的和
        for (int i = 0; i < len; i++) {//计数函数
            int count = 1;
            if (i != len -1) {
                while(nums[i+count] == nums[i]) {//此处可以产生越界
                    count++;
                }
            }
            temp.push_back(nums[i]);
            sum.push_back(nums[i] * count);
            //printf("%d %d %d\n", nums[i], nums[i] * count, i + count -1);
            i = i + count - 1;//之后还要再执行一次for+1
        }
        int len1 = temp.size();
        if (len1 == 1) 
            return sum[0];
        vector<int> dp(len1);
        dp[0] = sum[0];
        for (int i = 1; i < len1; i++) {
            if (temp[i] != temp[i-1] + 1)//保证选择的前一个数字不能比后一个小一
                dp[i] = dp[i-1] + sum[i];
            else {//i=1时会造成越界
                if(i != 1)
                    dp[i] = max(dp[i-2] + sum[i], dp[i-1]);
                else
                    dp[i] = max(sum[i], dp[i-1]);
            }
            //printf("%d\n", dp[i]);
        }
        return dp[len1-1];
        //return 0;
    }       
};
```

**官方做法是计算出最大值maxval，构造一个vector<int> sum设置对应的关系为在位置i的和为sum[i]；接下来就是对于sum进行动态规划，不选择相邻的数据**

```
class Solution {
private:
    int rob(vector<int> &nums) {
        int size = nums.size();
        int first = nums[0], second = max(nums[0], nums[1]);
        for (int i = 2; i < size; i++) {
            int temp = second;
            second = max(first + nums[i], second);
            first = temp;
        }
        return second;
    }

public:
    int deleteAndEarn(vector<int> &nums) {
        int maxVal = 0;
        for (int val : nums) {
            maxVal = max(maxVal, val);
        }
        vector<int> sum(maxVal + 1);
        for (int val : nums) {
            sum[val] += val;
        }
        return rob(sum);
    }
};

```

