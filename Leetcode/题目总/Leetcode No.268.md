# Leetcode No.268

###### [268. 丢失的数字](https://leetcode-cn.com/problems/missing-number/)

方法一：使用set记录数据，然后判断那个没有然后输出结果

方法二：直接计算所有数据的和，然后利用 0 - (n-1) 求和公式减去和就是结果

```c++
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int len = nums.size(), res = 0;
        for (int i = 0; i < len; i++) {
            res += nums[i];
        }

        return (len + 1) * len / 2 - res; 
    }
};
```

