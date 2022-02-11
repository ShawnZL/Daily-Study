# Leetcode No.72

###### [72. 编辑距离](https://leetcode-cn.com/problems/edit-distance/)

方法一：[讲解链接](https://leetcode-cn.com/problems/edit-distance/solution/bian-ji-ju-chi-by-leetcode-solution/)

本质不同的操作实际上只有三种：对于单词A插入一个字符；在单词B中插入一个字符；修改单词A的一个字符。

这样就可以利用动态规划解决问题，利用dp\[i][j]表示A的前i个字母和B的前j个字母之间的编辑距离，我们由此获得dp\[i][j - 1]，dp\[i - 1][j]，dp\[i - 1][j - 1]就可以计算dp\[i][j]。

dp\[i][j-1]为 A 的前 i 个字符和 B 的前 j - 1 个字符编辑距离的子问题。即对于 B 的第 j 个字符，我们在 A 的末尾添加了一个相同的字符，那么 dp\[i][j] 最小可以为 dp\[i][j-1] + 1；

dp\[i-1][j] 为 A 的前 i - 1 个字符和 B 的前 j 个字符编辑距离的子问题。即对于 A 的第 i 个字符，我们在 B 的末尾添加了一个相同的字符，那么 dp\[i][j] 最小可以为 dp\[i-1][j] + 1；

dp\[i-1][j-1] 为 A 前 i - 1 个字符和 B 的前 j - 1 个字符编辑距离的子问题。即对于 B 的第 j 个字符，我们修改 A 的第 i 个字符使它们相同，那么 dp\[i][j] 最小可以为 dp\[i-1][j-1] + 1。特别地，如果 A 的第 i 个字符和 B 的第 j 个字符原本就相同，那么我们实际上不需要进行修改操作。在这种情况下，dp\[i][j] 最小可以为 dp\[i-1][j-1]。

A和B的最后一个字母相同
$$
dp[i][j]=1+min(dp[i][j-1],dp[i-1][j],dp[i-1][j-1]-1)
$$
A和B最后一个字母不相同
$$
dp[i][j]=1+min(dp[i][j-1],dp[i-1][j],dp[i-1][j-1])
$$
对于边界情况，一个空串和一个非空串的编辑距离为 dp\[i][0] = i 和 dp\[0][j] = j，dp\[i][0] 相当于对 word1 执行 i 次删除操作，dp\[0][j] 相当于对 word1执行 j 次插入操作。

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int m = word1.size(), n = word2.size();
        if (m * n == 0) return m + n;
        vector<vector<int>> dp(m + 1, vector<int>(n + 1));
        
        // 初始化边界条件
        for (int i = 0; i <= m; i++) 
            dp[i][0] = i;
        for (int j = 0; j <= n; j++) 
            dp[0][j] = j;
        
        //计算
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                int left = dp[i - 1][j] + 1;
                int down = dp[i][j - 1] + 1;
                int left_down = dp[i - 1][j - 1];
                if (word1[i - 1] != word2[j - 1]) left_down += 1;
                dp[i][j] = min(left, min(down, left_down));
            }
        }
        //方法二
        /*for (int i = 1; i <= m; i++) {
            char c1 = word1[i - 1];
            for (int j = 1; j <= n; j++) {
                char c2 = word2[j - 1];
                if (c1 == c2)
                    dp[i][j] = min(dp[i - 1][j] + 1, min(dp[i - 1][j - 1], dp[i][j - 1] + 1));
                else
                    dp[i][j] = min(dp[i - 1][j - 1], min(dp[i - 1][j], dp[i][j - 1])) + 1;
            }
        }*/
        
        return dp[m][n];
    }
};
```

