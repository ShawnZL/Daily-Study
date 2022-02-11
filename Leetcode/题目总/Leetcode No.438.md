# Leetcode No.438

###### [438. 找到字符串中所有字母异位词](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

方法一：使用map，利用map统计子串的数量然后和目标串比较，但是注意一个问题是map中对应值为0时，不会自动删除，这样就会产生问题。

```c++
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        int lenp = p.size();
        int lens = s.size();
        map<char, int> countp, counts;
        for (int i = 0; i < lenp; ++i) {
            countp[p[i]] += 1;
            counts[s[i]] += 1;
        }
        vector<int> res;
        int flag = 0;
        if (countp == counts) res.push_back(flag);
        for (int i = lenp; i < lens; ++i) {
            /* 从前代码，没有删除以前的存在的数值
            counts[s[flag++]] -= 1;
           */
            if (counts[s[flag]] == 1) {
                counts.erase(s[flag]);
                ++flag;
            }
            else {
                counts[s[flag++]] -= 1;
            }
            counts[s[i]] += 1;
            if (counts == countp)
                res.push_back(flag);
        } 
        return res;
    }
};
```

```python
class Solution:
    def findAnagrams(self, s: str, p: str) -> List[int]:
        n, m, res = len(s), len(p), []
        if n < m: return res;
        p_cnt = [0] * 26;
        s_cnt = [0] * 26;
        for i in range(m):
            p_cnt[ord(p[i]) - ord('a')] += 1
            s_cnt[ord(s[i]) - ord('a')] += 1
        if s_cnt == p_cnt:
            res.append(0)
        
        for i in range(m, n):
            s_cnt[ord(s[i - m]) - ord('a')] -= 1
            s_cnt[ord(s[i]) - ord('a')] += 1
            if s_cnt == p_cnt:
                res.append(i - m + 1)
        return res
```

