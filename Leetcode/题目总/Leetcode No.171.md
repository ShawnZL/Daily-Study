# Leetcode No.171

[Excel表列序号](https://leetcode-cn.com/problems/excel-sheet-column-number/submissions/)

将这个视为26进制

```c++
class Solution {
public:
    int titleToNumber(string columnTitle) {
        int len = columnTitle.size();
        int res = 0;
        for (int i = 0; i < len; i++) {
            res = res * 26 + (columnTitle[i] - 'A' + 1);
        }
        return res;
    }
};
```

sum = sum * 26 - 'A' + 1 + c 这么写的话，注意把减放在加前面，或者加括号，防止爆int