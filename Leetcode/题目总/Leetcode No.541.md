# Leetcode No.541

###### [541. 反转字符串 II](https://leetcode-cn.com/problems/reverse-string-ii/)

设置一个flag,我们就按照每k个数据循环一次，但是按照flag判断是否翻转，在翻转中我设置了一个end表示最后一个变量，但是有可能会出现越界，所以我设置了一个min(n, (i + 1) * k) - 1。表示数据。

```c++
class Solution {
public:
    string reverseStr(string s, int k) {
        int n = s.length();
        int count = n /  k;
        int flag = true;// 表示要翻转
        for (int i = 0; i < count; i++ ) {
            if (flag) {
                int start = i * k;
                int end = min((i + 1) * k, n) - 1;
                //printf("%d %d\n", start, end);
                while(start < end) {
                    swap(s[start++], s[end--]);
                }
            }
            flag = !flag;
        }
        if (flag && count * k < n) {
            int start = count * k;
            int end = n - 1;
            while(start < end) {
                swap(s[start], s[end]);
                start++; end--;
            }
        }
        return  s;
    }
};
```

