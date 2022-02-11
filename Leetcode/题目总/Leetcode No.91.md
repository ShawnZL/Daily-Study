# Leetcode No.91

###### [91. 解码方法](https://leetcode-cn.com/problems/decode-ways/)

本题目我的初始解法就是分开一步一步计算。

```c++
class Solution {
public:
    int numDecodings(string s) {
        int n = s.size();
        vector<int> dp(n);
        //dp[i]表示以s[i]结尾可以记录多少数据
        //转移dp[i] = dp[i - 2] + dp[i - 1]
        if (s[0] == '0') 
            dp[0] = 0;
        else dp[0] = 1;
        for (int i = 1; i < n; i++) {
            int x = s[i] - '0';
            int y = s[i - 1] - '0';
            int yx = y * 10 + x;
            if ( 0 < yx && yx < 27) {
                if (yx == 10 || yx == 20) {//例如210 101
                    if (i > 1)
                        dp[i] = dp[i - 2];
                    else
                        dp[i] = dp[i - 1];
                }
                else if (yx < 10) 
                    dp[i] = dp[i - 1];
                else if (i > 1)
                    dp[i] = dp[i - 2] + dp[i - 1];
                else
                    dp[i] = dp[i - 1] + 1;
            }
            else if (yx > 26) {
                if (x == 0)
                    return 0;
                else
                    dp[i] = dp[i - 1];
            }
            else {//yx == 00
                return 0;
            }
            //printf("%d ", dp[i]);
        }
        return dp[n - 1];
    }
};
```

官方解答

我们判断是否可以加 dp [i - 1] 和 dp [i - 2]

```c++
class Solution {
public:
    int numDecodings(string s) {
        int n = s.size();
        vector<int> f(n + 1);
        f[0] = 1;
        for (int i = 1; i <= n; ++i) {
            if (s[i - 1] != '0') {
                f[i] += f[i - 1];
            }
            if (i > 1 && s[i - 2] != '0' && ((s[i - 2] - '0') * 10 + (s[i - 1] - '0') <= 26)) {
                f[i] += f[i - 2];
            }
        }
        return f[n];
    }
};
```

