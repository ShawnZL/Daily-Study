# Leetcode No.139

###### [139. 单词拆分](https://leetcode-cn.com/problems/word-break/)

动态规划

dp[i] = dp[j] && check(s[j..i-1])

其中check*(*s*[*j*..*i*−1]) 表示子串 *s*[*j*..*i*−1] 是否出现在字典中。

```c++
class Solution {
public:
    bool wordBreak(string s, vector<string>& wordDict) {
        unordered_set<string> wordDictset;
        for (auto word: wordDict) {
            wordDictset.insert(word);
        }

       vector<bool> dp(s.size() + 1);
       dp[0] = true;
       int len = s.size();
       for (int i = 1; i <= len; i++) {
           for (int j = 0; j < i; j++) {
               if (dp[j] && wordDictset.find(s.substr(j, i - j)) != wordDictset.end()) {//长度
                    dp[i] = true;
                    break;
               }
           }
       }
       return dp[s.size()];
    }
};
```

