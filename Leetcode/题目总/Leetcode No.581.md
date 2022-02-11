# Leetcode No.581

[[581. 最短无序连续子数组](https://leetcode-cn.com/problems/shortest-unsorted-continuous-subarray/)]

说实话，没有看懂题目什么意思

我们将给定的数组nums 表示为三段子数组拼接的形式，分别记作numsA，numsB，numsC。当我们对numsB进行排序，整个数组将变为有序。换而言之，当我们对整个序列进行排序，numsA和 numsC都不会改变。

本题要求我们找到最短的numsB，即找到最大的numsA和numsC的长度之和。因此我们将原数组nums 排序与原数组进行比较，取最长的相同的前缀numsA，取最长的相同的后缀为numsC，这样我们就可以取到最短的numsB。

```c++
class Solution {
public:
    int findUnsortedSubarray(vector<int>& nums) {
        if (is_sorted(nums.begin(), nums.end())) 
            return 0;
        vector<int> numsSorted(nums);
        sort(numsSorted.begin(), numsSorted.end());
        int left = 0, right = nums.size() - 1;
        while (nums[left] == numsSorted[left])
            left++;
        while (nums[right] == numsSorted[right])
            right--;
        
        return right - left + 1;
    }
};
```

方法二：numsB在nums对应的区间为[left, right]

同时numsB和numsC中任意一个数字都大于等于numsA中任何一个数字，所以在[i+1, n-1]范围里
$$
nums[i] <= nums[j]
$$
对于[2, 6, 4, 8, 10, 9, 15], 先更新最小值，在更新位置

maxn : 2    6     6     8     10    10    15

right   : -1   -1    2     2      2      5      5

minn  : 15    9    9     8      4      4      2

left     :  -1     -1    4    4      4      1      1

```c++
class Solution {
public:
    int findUnsortedSubarray(vector<int>& nums) {
        int n = nums.size();
        int maxn = INT_MIN, right = -1;
        int minn = INT_MAX, left = -1;
        for (int i = 0; i < n; i++) {
            if (maxn > nums[i]) {//在right的范围里，maxn都是最大
                right = i;
            } else {
                maxn = nums[i];
            }
            if (minn < nums[n - i - 1]) {//在left的范围里，minn最小
                left = n - i - 1;
            } else {
                minn = nums[n - i - 1];
            }
        }
        return right == -1 ? 0 : right - left + 1;
    }
};

```







