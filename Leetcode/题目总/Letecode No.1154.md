# Letecode No.1154

#### [1154. 一年中的第几天](https://leetcode-cn.com/problems/day-of-the-year/)

直接计算

**注意string 转 int 类型**

```
class Solution {
public:
    const int mon[2][12] = {{31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31},
                {31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31}};
    int dayOfYear(string date) {
        int flag = 0;
        int year = stoi(date.substr(0, 4));
        int month = stoi(date.substr(5, 2));
        int day = stoi(date.substr(8, 2));
        if (year % 400 == 0 || (year % 100 != 0  && year % 4 == 0))
            flag = 1;
        int res = 0;
        for (int i = 0; i < month - 1; i++) {
            res += mon[flag][i];
        }
        res += day;
        return res;
    }
};
```

