# Leetcode No.58

###### [58. 最后一个单词的长度](https://leetcode-cn.com/problems/length-of-last-word/)

本题简单，直接从后边开始遍历，先将空格隔过去，之后直接统计数据,

**重要一点就是在使用while循环，一切循环都要先判断是否越界，如果有越界风险，我们一定要先判断是否越界再取判断其他条件**

```c++
class Solution {
public:
    int lengthOfLastWord(string s) {
        int n = s.length();
        int res = 0, i = n - 1;
            while(s[i] == ' ') i--;
            while(i >= 0 && s[i] != ' ') {
                res++;
                i--;
            }
        return res;
    }
};
```

