# Leetcode No.953

#### [953. 验证外星语词典](https://leetcode.cn/problems/verifying-an-alien-dictionary/)

```
class Solution {
public:
    bool isAlienSorted(vector<string>& words, string order) {
        unordered_map<char, int> order_res;
        for (int i = 0; i < order.size(); ++i) {
            order_res[order[i]] = i;
        }
        for (int i = 0; i < words.size() - 1; ++i) {
            string & cur = words[i];
            string & next = words[i + 1];
            bool res = false;
            for (int j = 0; j < min(cur.size(), next.size()); ++j) {
                if (order_res[cur[j]] < order_res[next[j]]) {
                    res = true; //表示本次结果正确
                    break;
                }
                else if (order_res[cur[j]] > order_res[next[j]]) {
                    return false;
                }
            }
            // 如果长度不一样，短的在前
            if (!res && cur.size() > next.size())
                return false;
        }
        return true;
    }
};
```

