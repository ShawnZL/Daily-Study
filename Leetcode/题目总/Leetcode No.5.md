# Leetcode No.5



方法一：动态规划，首先我们明确，每一个单个的字母是长度为1的回文串，所以存在第一个for循环过程

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.length();
        if (n < 2) return s;
        vector<vector<int>> dp(n, vector<int>(n, 0));
        int start = 0, maxlen = 1;

        for (int i = 0; i < n; i++) {
            dp[i][i] = 1;
        }

        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (s[j] == s[i])
                    dp[j][i] = dp[j + 1][i - 1] + 2;
                else
                    dp[j][i] = dp[j + 1][i - 1];
                //出问题地方就在于这里，因为我每次都保存可以上一次结果，中间可以不连续匹配！
                if (dp[j][i] > maxlen) {
                    start = j;
                    maxlen = dp[j][i];
                }
                printf("%d ", dp[j][i]);
            }
            printf("\n");
        }
        return s.substr(start, maxlen);
    }
};
```

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.length();
        if (n < 2) return s;
        vector<vector<int>> dp(n, vector<int>(n, 0));
        int start = 0, maxlen = 1;

        for (int i = 0; i < n; i++) {
            dp[i][i] = 1;
        }

        for (int i = 1; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (s[j] == s[i])
                    //改进地方就这这里，多加了一个判读语句
                    if (dp[j + 1][i - 1] != 0 || i - j == 1)
                        dp[j][i] = dp[j + 1][i - 1] + 2;
                else
                    dp[j][i] = 0;
                //出问题地方就在于这里，因为我每次都保存可以上一次结果，中间可以不连续匹配！
                if (dp[j][i] > maxlen) {
                    start = j;
                    maxlen = dp[j][i];
                }
                //printf("%d ", dp[j][i]);
            }
            //printf("\n");
        }
        return s.substr(start, maxlen);
    }
};
```

