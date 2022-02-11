# Leetcode No.1816

###### [1816. 截断句子](https://leetcode-cn.com/problems/truncate-sentence/)

先找到可以截取的位置，然后对于这个位置进行操作！

```
class Solution {
public:
    string truncateSentence(string s, int k) {
        int flag = 0, loc = 0, len = s.size();
        while (loc < len && flag < k) {
            if (s[loc] == ' ')
                flag++;
            ++loc;
        }
        return loc < len ? s.substr(0, loc - 1): s.substr(0, loc);
    }
};
```

