# Leetcode No.434

###### [434. 字符串中的单词数](https://leetcode-cn.com/problems/number-of-segments-in-a-string/)

方法一：

```c++
class Solution {
public:
    int countSegments(string s) {
        int len = s.length(), res = 0;
        if (len == 0) return 0;
        for (int i = 1; i < len; i++) {
            if (s[i] == ' ' && s[i - 1] != ' ')
                res++;
        }
        if (s[len - 1] != ' ') return res + 1;
        return res;
    }
};
```

方法二：

利用||和&&的判断特性，||先判断第一个，只要第一个满足条件，第二个不必再判断。&&先判断第一个，只要第一个不满足情况，就不再继续下去

```c++
class Solution {
public:
    int countSegments(string s) {
        int len = s.length(), res = 0;
        for (int i = 0; i < len; i++) {
            if ((i == 0 || s[i - 1] == ' ') && s[i] != ' ')
                res++;
        }
        return res;
    }
};
```

