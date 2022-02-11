# Leetcode No.96

###### [96. 不同的二叉搜索树](https://leetcode-cn.com/problems/unique-binary-search-trees/)

动态规划经典题目，设置边界条件dp[0] = 1, dp[1] = 1，表示形态只有一个。从0开始到 i - 1个节点，表示为左边节点的个数，同时有比年节点个数就为 i - 1 - left, 在当前情况下的形态个数为 dp[left] * dp[right]

转移条件为dp[i] += (dp[left] * dp[right])

```c++
class Solution {
public:
    int numTrees(int n) {
        vector<int> dp(n + 1);
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            for (int j = 0; j < i; j ++) {
                dp[i] += (dp[j] * dp[i - 1 - j]);
            }
        }
        return dp[n];
    }
};
```

