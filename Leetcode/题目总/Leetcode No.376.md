# Leetcode No.376

###### [376. 摆动序列](https://leetcode-cn.com/problems/wiggle-subsequence/)

方法一：动态规划

建立两个数组 up\[i]表示前 i 个元素中某一个为结尾的最长上升摆动长度

down表示前 i 个元素中某一个为结尾的最长下降摆动长度

当nums[i - 1] < nums[i] 我们可以从up\[i - 1]进行转移，也可以从down\[i - 1]进行转移，上升加一，降低不变

当nums[i - 1] >= nums[i] 我们可以从up\[i - 1]进行转移，也可以从down\[i - 1]进行转移，上升不变，降低减一

[解析链接](https://leetcode-cn.com/problems/wiggle-subsequence/solution/bai-dong-xu-lie-by-leetcode-solution-yh2m/)

```c++
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        int n = nums.size();
        if (n < 2) 
            return n;
        vector<int> up(n), down(n);
        up[0] = down[0] = 1;
        for (int i = 1; i < n; i++) {
            if (nums[i - 1] > nums[i]) {
                up[i] = up[i - 1];
                down[i] = max(up[i - 1] + 1, down[i - 1]);
            }
            else if (nums[i - 1] < nums[i]) {
                up[i] = max(up[i - 1], down[i - 1] + 1);
                down[i] = down[i - 1];
            }
            else {
                up[i] = up[i - 1];
                down[i] = down[i - 1];
            }
        }
        return max(down[n - 1], up[n - 1]);
    }
};
```

空间优化结果

```c++
class Solution {
public:
    int wiggleMaxLength(vector<int>& nums) {
        int n = nums.size();
        if (n < 2) 
            return n;
        int up = 1, down = 1;
        for (int i = 1; i < n; i++) {
            if (nums[i] > nums[i - 1])
                up = max(up, down + 1);
            else if (nums[i] < nums[i - 1])
                down = max(down, up + 1);
        }
        return max(up, down);
    }
};
```

