# Leetcode offer 53-I

本题目地址[leetcode](https://leetcode-cn.com/problems/zai-pai-xu-shu-zu-zhong-cha-zhao-shu-zi-lcof/)

方法一：直接求解

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int len = nums.size(), res =0;
        for(int i = 0; i < len; i++) {
            if(nums[i] > target) break;
            else if(nums[i] == target) res++;
        }
        return res;
    }
};
```

方法二：使用二分查找，根据整个数组是单调递增的。leftIdx是寻找第一个等于target的位置，rightIdx寻找第一个大于target的下标。出现次数就为rightIdx - leftIdx + 1。

```c++
class Solution {
public:
    int binarySearch(vector<int>& nums, int target, bool lower) {
        //为leftIdx时lower为 true
        int left = 0, right = (int)nums.size() - 1, ans = (int)nums.size();
        while(left <= right) {
            int mid = (left + right) / 2;
            if (nums[mid] > target || (low && nums[mid] >= target)) {
                right = mid - 1;
                ans = mid;
            } else {
                left = mid + 1;
            }
        }
        return ans;
    }
    int search(vector<int>& nums, int target) {
        int leftIdx = binarySearch(nums, target, true);//第一个大于等于target
        int rihgtIdx = binarySearch(nums, target, false) - 1;//第一个大于target
        if (leftIdx <= rihgtIdx && rihgtIdx < nums.size() && nums[leftIdx] == target && nums[rihgtIdx] == target) {
            return rihgtIdx - leftIdx + 1;
        }
        return 0;
    }
};
```

**！！！rightIdx为第一个大于target的数据，所以不要忘记减一**