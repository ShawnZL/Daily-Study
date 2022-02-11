# Leetcode No.500

###### [500. 键盘行](https://leetcode-cn.com/problems/keyboard-row/)

方法一：使用map记录所有的数据所在的行数，只有有一个字母和第一个字母所在行数不一样，就直接不再判断！

另外注意大小写的问题，使用函数 tolower() 转换为小写， toupper() 转换为大写！

```c++
class Solution {
public:
    vector<string> alp = {"qwertyuiop", "asdfghjkl", "zxcvbnm"};
    vector<string> findWords(vector<string>& words) {
        vector<string> ans;
        int len = words.size();
        map<char, int> count;
        for (int i = 0; i < 3; i++) {
            int temp_len = alp[i].size();
            for (int j = 0; j < temp_len; j++) {
                count[alp[i][j]] = i;
                //printf("%c=%d ", alp[i][j], count[alp[i][j]]);
            }
        }

        for (int i = 0; i < len; i++) {
            int temp_len = words[i].size();
            int flag = count[tolower(words[i][0])];
            for (int j = 1; j < temp_len; j++) {
                char c = tolower(words[i][j]);
                //printf("%c ", c);
                if (count[tolower(words[i][j])] != flag) {
                    flag = -1;
                    break;
                }
            }
            if (flag != -1)
                ans.push_back(words[i]);
        }
        return ans;
    }
};
```

