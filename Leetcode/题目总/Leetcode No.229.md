# Leetcode No.229

###### [229. 求众数 II](https://leetcode-cn.com/problems/majority-element-ii/)

方法一：利用hash统计每一个数字出现。然后遍历map，输出结果

```c++
class Solution {
public:
    vector<int> majorityElement(vector<int>& nums) {
        map<int, int> counts;
        vector<int> res;
        int len = nums.size();
        if (len == 1) return nums;
        for (int i = 0; i < len; i++) {
            counts[nums[i]] += 1;
        }
        for (auto &v: counts) {
            if (v.second > len / 3) 
                res.push_back(v.first);
        }
        return res;
    }
};
```

这里最重要的就是选择合适的遍历map数组的方式！

```
for (auto &v: counts) {
	if (v.second > len / 3)
		res.push_back(v.first);
}
```

