# Leetcode No.3

[无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/submissions/)

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_set<char> occ;
        int n = s.size();
        int rk = -1, ans = 0;
        //枚举左指针的位置，初始值隐性地表示为-1
        for (int i = 0; i < n; ++i) {
            if (i != 0) {
                //左指针向右移动一格，移除一个字符
                occ.erase(s[i - 1]);
            }
            while (rk + 1 < n && !occ.count(s[rk + 1])) {
                occ.insert(s[rk + 1]);
                ++rk;
            }
            ans = max(ans, rk - i + 1);
        }
        return ans;
    }
};
```

