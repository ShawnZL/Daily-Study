# Leetcode No.1218

###### [1218. 最长定差子序列](https://leetcode-cn.com/problems/longest-arithmetic-subsequence-of-given-difference/)

方法一：动态规划 但是有问题就是在面对大数据的时候会超出时间限制！

边界条件 dp[0] = 1

状态转移方程：dp[i] = max(dp[i], dp[j] + 1)

```c++
class Solution {
public:
    //动态规划
    int longestSubsequence(vector<int>& arr, int difference) {
        int len = arr.size(), res = 1;
        vector<int> dp(len);
        dp[0] = 1;
        for (int i = 1; i < len; i++) {
            dp[i] = 1;
            for (int j = 0; j < i; j ++) {
                if (arr[i] - arr[j] == difference) {
                    dp[i] = max(dp[i], dp[j] + 1); 
                }
            }
            res = max(dp[i], res);
        }
        return res;
    }
};
```

改进写法！！

令dp [i] 表示以 arr [i] 为结尾的最长的当差子序列长度，我们可以在arr [i] 左侧找到满足 arr [j] = arr[i] - d 的元素，将 arr [i] 加到以 arr [j] 为结尾的最长的等差子序列末尾，这样可以递推地从dp [j] 计算到 dp[i]。

由于我们是从左往右遍历 arr 的，对于两个相同的元素，下标较大的元素对应的 dp 值不会小于下标较小的元素对应的 dp 值，因此下标 j 可以取满足 j<i 且 arr [j] = arr [i] − d 的所有下标的最大值。故有转移方程
$$
dp[i]=dp[j]+1
$$
由于我们总是在左侧找一个最近的等于 arr[i]−d 元素并取其对应 dp 值，因此我们直接用 dp[v] 表示以 v 为结尾的最长的等差子序列的长度，这样 dp[v−d] 就是我们要找的左侧元素对应的最长的等差子序列的长度，因此转移方程可以改为
$$
dp[v]=dp[v-d]+1
$$


```c++
class Solution {
public:
    int longestSubsequence(vector<int> &arr, int difference) {
        int ans = 0;
        unordered_map<int, int> dp;
        for (int v: arr) {
            dp[v] = dp[v - difference] + 1;
            ans = max(ans, dp[v]);
        }
        return ans;
    }
};
```

