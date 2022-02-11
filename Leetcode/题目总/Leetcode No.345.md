# Leetcode No.345

###### [345. 反转字符串中的元音字母](https://leetcode-cn.com/problems/reverse-vowels-of-a-string/)

方法一：本题目我的思路首先是记录下原因字母的位置，再交换即可

```c++
class Solution {
public:
    string reverseVowels(string s) {
        int n = s.size();
        vector<int> local;
        for (int i = 0; i < n; i++) {
            if (s[i] == 'a' || s[i] == 'e' || s[i] == 'i' || s[i] == 'o' || s[i] == 'u') 
                local.push_back(i);
            if (s[i] == 'A' || s[i] == 'E' || s[i] == 'I' || s[i] == 'O' || s[i] == 'U') 
                local.push_back(i);
        }
        int m = local.size();
        for (int i = 0; i < m / 2; i++) {
            char temp = s[local[i]];
            s[local[i]] = s[local[m - i - 1]];
            s[local[m - i - 1]] = temp;
        }
        return s;
    }
};
```

双指针法：

```c++
class Solution {
public:
    string reverseVowels(string s) {
        auto isVowel = [vowels = "aeiouAEIOU"s](char ch) {
            return vowels.find(ch) != string::npos;
        };

        int n = s.size();
        int i = 0, j = n - 1;
        while (i < j) {
            while (i < n && !isVowel(s[i])) {
                ++i;
            }
            while (j > 0 && !isVowel(s[j])) {
                --j;
            }
            if (i < j) {
                swap(s[i], s[j]);
                ++i;
                --j;
            }
        }
        return s;
    }
};
```

