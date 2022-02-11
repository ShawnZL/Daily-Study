# Leetcode No.162

###### [162. 寻找峰值](https://leetcode-cn.com/problems/find-peak-element/)

方法一：运行超时，在未满足时间限制log n 的情况下，

```c++
class Solution {
public:
    //log n 首先考虑二分法
    int findPeakElement(vector<int>& nums) {
        int n = nums.size();
        for (int i = 1; i < n - 1; i++) {
            if (nums[i] > nums[i - 1] && nums[i] > nums[i + 1]) {
                return i;
            }
            while (nums[i] > nums[i + 1]) {
                i++;
            }
            i--;
        }
        return 0; 
    }
};
```

方法二：直接查找其中最大值

```c++
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        return max_element(nums.begin(), nums.end()) - nums.begin();
    }
};
```

方法三：首先在 [0, n) 范围中随机选择一个初始位置 i, 随后根据 nums[i - 1]，nums[i]，nums[i + 1] 的关系确定朝那个方向走，

nums[i - 1] < nums[i] > nums[i + 1] 直接返回结果

nums[i - 1] < nums[i] < nums[i + 1] i 处于上坡，向右走

nums[i - 1] > nums[i] > nums[i + 1] i 处于下坡，向左走

nums[i - 1] > nums[i] < nums[i + 1] i 处于低谷，随机左右

我们规定最后一种情况往右走，当 i 不是峰值的时候

nums[i] < nums[i + 1] 向右， nums[i] > nums[i + 1] 向左

```c++
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int n = nums.size();
        int idx = rand() % n;
        // 辅助函数，输入下标 i，返回一个二元组 (0/1, nums[i])
        // 方便处理 nums[-1] 以及 nums[n] 的边界情况
        auto get = [&](int i) -> pair<int, int> {
            if (i == -1 || i == n) {
                return {0, 0};
            }
            return {1, nums[i]};
        };

        while (!(get(idx - 1) < get(idx) && get(idx) > get(idx + 1))) {
            if (get(idx) < get(idx + 1)) {
                idx++;
            }
            else
                idx--;
        }
        return idx;
    }
};
```

方法四：官方二分法

```c++
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int n = nums.size();
        int left = 0, right = n - 1;
        while (left < right) {
            int mid = (left + right) / 2;
            //此时无法满足右边条件，左边条件要变化为mid + 1
            if (nums[mid] < nums[mid + 1])
                left = mid + 1;
            //条件满足，直接固定右边界，
            else
                right = mid;
        }
        return left;
    }
};
```

