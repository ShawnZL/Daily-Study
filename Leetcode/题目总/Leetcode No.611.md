# Leetcode No.611

#### [611. 有效三角形的个数](https://leetcode-cn.com/problems/valid-triangle-number/)

直接模拟，但是超过时间限制

```c++
class Solution {
public:
    int triangleNumber(vector<int>& nums) {
        //判断三角形方法，两边之和大于第三边
        int len = nums.size();
        vector<vector<int>> sums(len, vector<int>(len));
        for (int i = 0; i < len; i++) {
            for (int j = i + 1; j < len; j++) {
                sums[i][j] = nums[i] + nums[j];
                sums[j][i] = nums[i] + nums[j];
            }
        }
        int ans = 0;
        for (int i = 0; i < len; i++) {
            for (int j = i + 1; j < len; j++) {
                for (int n = j + 1; n < len; n++) {
                    if (sums[i][j] > nums[n] && sums[i][n] > nums[j] && sums[j][n] > nums[i])
                    ans ++
                }
            }
        }
    }
};
```



官方方法一：对于边a, b, c，要满足两边之和大于第三边，我们这里将设置a<=b<=c，此时a+c>b和b+c>a必定成立，我们只要保证a+b>c。

这里采用二分查找[j+1, n-1]

```c++
class Solution {
public:
    int triangleNumber(vector<int>& nums) {
        int n = nums.size(), ans = 0;
        sort(nums.begin(), nums.end());
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                int left = j + 1, right = n - 1, k = j;
                while (left <= right) {
                    //此时记住二分的最终跳出循环条件为 left>right
                    int mid = (left + right) / 2;
                    if (nums[mid] < nums[i] + nums[j]) {
                        k = mid;//记录此时满足的情况，可能nums[left]就无法满足
                        left = mid + 1;
                    }
                    else {
                        right = mid - 1;
                    }
                }
                ans += k - j;
            }
        }
        return ans;
    }
};
```

方法二：

a = nums[i], b = nums[j]，当满足nums[k] < nums[i] + nums[j]的下标记为Kj,当i固定，j的不断增加，不等式右侧在增大，Kj也是在增大

我们每一次将j向右移动一个位置，即 j ← j + 1，并尝试不断向右移动k，使得k 是最大的满足 nums[k] < nums[i] + nums[j] 的下标。我们将max(k−j,0) 累加入答案。

k+1视为一个整体，nums[k+1]才会出现边界条件为k+1<n。

```c++
class Solution {
public:
    int triangleNumber(vector<int>& nums) {
        int n = nums.size();
        sort(nums.begin(), nums.end());
        int ans = 0;
        for (int i = 0; i < n; i++) {
            int k = i;
            for (int j = i + 1; j < n; j++) {
                while (k + 1 < n && nums[k + 1] < nums[i] + nums[j]) {
                    //此时先判定数组是否越界，因为&&是按照顺序判定你，只要一个不满足就退出
                    //k+1是一个整体条件。
                    k++;
                }
                //k-j的原因是除去j以外还有k-j个数字可以满足条件。
                ans += max(k - j, 0);
            }
        }
        return ans;
    }
};
```

