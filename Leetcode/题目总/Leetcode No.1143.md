# Leetcode No.1143

###### [1143. 最长公共子序列](https://leetcode-cn.com/problems/longest-common-subsequence/)

方法一：动态规划

我们分别创建 n1行 n2 列的二维数组dp,其中 dp\[i]\[j]表示 text\[0, i] 和 text[0, j] 的最长公共子序列的长度。

当 i = 0 时， text1[0, i] 为空，空字符串和任何字符串的最大公共子序列的长度都是0，所以dp\[i]\[0] = 0, 同理 dp\[0]\[j] = 0。 

当 text1[i] == text2[j]时，
$$
dp[i][j] = dp[i - 1][j - 1] + 1;
$$


当text1[i] != text2[j]时，考虑以下两项：*text*1[0:*i*−1] 和 text2[0:j]的最长公共子序列 和 *text*1[0:*i*] 和 text*2[0:*j*−1] 的最长公共子序列
$$
dp[i][j]=max(dp[i−1][j],dp[i][j−1])
$$

```c++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int m = text1.length(), n = text2.length();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1));
        for (int i = 1; i <= m; i++) {
            char c1 = text1.at(i - 1);
            for (int j = 1; j <= n; j++) {
                char c2 = text2.at(j - 1);
                if (c1 == c2) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[m][n];
    }
};

```

