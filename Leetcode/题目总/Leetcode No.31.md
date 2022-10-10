# Leetcode No.31

#### [31. 下一个排列](https://leetcode.cn/problems/next-permutation/)

1.首先从后向前查找第一个顺序对 (i,i+1)，满足 a[i]<a[i+1]。这样「较小数」即为 a[i]。此时 [i+1,n) 必然是下降序列。

2.如果找到了顺序对，那么在区间 [i+1,n) 中从后向前查找第一个元素 j 满足 a[i]<a[j]。这样「较大数」即为 a[j]。

3.交换 a[i] 与 a[j]，此时可以证明区间 [i+1,n) 必为降序。我们可以直接使用双指针反转区间 [i+1,n) 使其变为升序，而无需对该区间进行排序。

```
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int lens = nums.size();
        int i = lens - 2;
        while (i >= 0 && nums[i] >= nums[i + 1]) {
            i -= 1;
        }
        if (i >= 0) {
            int j = lens - 1;
            while (j >= 0 && nums[i] >= nums[j]) {
                j--;
            }
            swap(nums[i], nums[j]);
        }
        reverse(nums.begin() + i + 1, nums.end());
    }
};
```

