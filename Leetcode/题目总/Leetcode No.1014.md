# Leetcode No.1014

#### [1014. 最佳观光组合](https://leetcode-cn.com/problems/best-sightseeing-pair/)

本题目使用动态规划做法

时间复杂度为O(n^2) 无法通过所有例子

```c++
class Solution {
public:
    int maxScoreSightseeingPair(vector<int>& values) {
        int len = values.size();
        vector<int> dp(len, 0);
        //表示以i结尾的最大组合
        for (int i = 0; i < len; i++) {
            for (int j = 0; j < i; j++) {
                dp[i] = max(dp[i], values[j] + values[i] + j - i);
            }
        }
        int ans = *max_element(dp.begin(), dp.end());
        return ans;
    }
};
```

本题目将公式
$$
values[i] + values[j] + i - j
$$
其中拆分为
$$
values[i] + i; values[j] - j;
$$
在相对于values[j] - j 不变的情况下我们保证在[0, j]中，就是**values[i] + i**最大。

```c++
class Solution {
public:
    int maxScoreSightseeingPair(vector<int>& values) {
        int n = values.size(), ans = 0;
        //将values[i] + i 和 values[j] - j 拆分开来
        vector<int> dp(n);
        dp[0] = values[0] + 0;
        for (int i = 1; i < n; i++) {
            ans = max(ans, dp[i - 1] + values[i] - i);
            dp[i] = max(dp[i - 1], values[i] + i);
            //printf("%d %d\n", ans, dp[i]);
        }
        return ans;
    }
};
```

空间优化后

```c++
class Solution {
public:
    int maxScoreSightseeingPair(vector<int>& values) {
        int ans = 0, mx = values[0] + 0;
        for (int j = 1; j < values.size(); ++j) {
            ans = max(ans, mx + values[j] - j);
            // 边遍历边维护
            mx = max(mx, values[j] + j);
        }
        return ans;
    }
};
```

