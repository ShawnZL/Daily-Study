# Leetcode No.1567

#### [1567. 乘积为正数的最长子数组长度](https://leetcode-cn.com/problems/maximum-length-of-subarray-with-positive-product/)

动态维护两个数组*positive* 和*negative* *positive*[i]表示以下标i结尾的乘积为正数的的最长子串长度，*negative*[i]表示乘积为负数的最长子数组长度

nums[i] > 0
$$
positive[i] = positive[i-1] + 1; negative[i] = negative[i-1]+1(negative[i-1] > 0) or  0(negative[i-1] = 0)
$$
nums[i] < 0
$$
positive[i] = negative[i-1]+1(negative[i-1]>0) or 0 (negative[i-1]=0) negative[i] = positive[i-1]+1
$$


```c++
class Solution {
public:
    int getMaxLen(vector<int>& nums) {
        int length = nums.size();
        vector<int> positive(length), negative(length);
        if (nums[0] > 0) {
            positive[0] = 1;
        }
        else if (nums[0] < 0) {
            negative[0] = 1;
        }
        int maxLength = positive[0];
        for (int i = 1; i < length; ++i) {
            if (nums[i] > 0) {
                positive[i] = positive[i - 1] + 1;
                negative[i] = (negative[i - 1] > 0 ? negative[i - 1] + 1 : 0);
            }
            else if (nums[i] < 0) {
                positive[i] = (negative[i - 1] > 0 ? negative[i - 1] + 1 : 0);
                negative[i] = positive[i - 1] + 1;
            }
            else {
                positive[i] = 0;
                negative[i] = 0;
            }
            maxLength = max(maxLength, positive[i]);
        }
        return maxLength;
    }
};

```

