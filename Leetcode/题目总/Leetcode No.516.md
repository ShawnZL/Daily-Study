# Leetcode No.516

###### [516. 最长回文子序列](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

dp\[i][j] 表示字符串s的下标范围\[i, j]最长的回文串长度，dp\[i][i] = 1;

当i < j 时 dp\[i][j] 需要分别考虑s[i] 和 s[j]相等 和 不相等的情况

1. s[i] = s[j] s在[i+1, j-1] 内的最长回文子序列，然后在该子序列尾部加入s\[i]  s[j], dp\[i][j] = dp\[i + 1][j - 1] + 2

2. s[i] != s[j] dp\[i][j] = max(dp\[i + 1][j], dp\[i][j - 1]);

   由于状态转移方程都是从长度较短的子序列向长度较长的子序列转移

```c++
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        int n = s.size();
        vector<vector<int>> dp(n, vector<int>(n));
        for (int i = n - 1; i >= 0; i--) {
            dp[i][i] = 1;
            char c1 = s[i];
            for (int j = i + 1; j < n; j++) {
                char c2 = s[j];
                if (c1 == c2) 
                    dp[i][j] = dp[i + 1][j - 1] + 2;
                else
                    dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
            }
        }
        return dp[0][n - 1];
    }
};
```

