# Leetcode No.187

###### [187. 重复的DNA序列](https://leetcode-cn.com/problems/repeated-dna-sequences/)

方法一：哈希表

我们可以用一个哈希表统计 s 所有长度为 10 的子串的出现次数，返回所有出现次数超过 10 的子串。

代码实现时，可以一边遍历子串一边记录答案，为了不重复记录答案，我们只统计当前出现次数为 2 的子串。

```c++
class Solution {
    const int L = 10;
public:
    vector<string> findRepeatedDnaSequences(string s) {
        int len = s.size();
        vector<string> ans;
        unordered_map<string, int> cnt;
        for (int i = 0; i <= len - L; i++) {
            string sub = s.substr(i, L);
            if (++cnt[sub] == 2) {
                ans.push_back(sub);
            }
        }
        return ans;
    }
};
```

方法二：哈希表+滑动窗口+位运算

由于s中只含有 4 种字符，我们可以将每个字符用2比特表示 A : 00, C : 01, G : 10, T : 11 

如此一来一个长度为 10 的字符串就可以用 20 个比特表示。这为字符串到整数的映射，每一个整数都对应唯一个字符串，因此我们可以将方法一中的哈希表改为存储每个长为 10 的子串的整数表示。为了优化时间复杂度，我们可以使用一个固定大小为10的滑动窗口来计算子串的整数表示。设当前滑动窗口对应的整数表示为 xx，当我们要计算下一个子串时，就将滑动窗口向右移动一位，此时会有一个新的字符进入窗口，以及窗口最左边的字符离开窗口，这些操作对应的位运算，

1.滑动窗口向右移动一位：`x = x << 2`，由于每个字符用 22 个比特表示，所以要左移 2 位。

2.一个新的字符 ch 进入窗口：x = x | bin[ch]，这里 bin[ch] 为字符 ch 的对应二进制。

3.窗口最左边的字符离开窗口：x = x & ((1 << 20) - 1)，由于我们只考虑 x 的低 2020 位比特，需要将其余位置零，即与上 (1 << 20) - 1。

```c++
class Solution {
    const int L = 10;
    unordered_map<char, int> bin = {{'A', 0}, {'C', 1}, {'G', 2}, {'T', 3}};
public:
    vector<string> findRepeatedDnaSequences(string s) {
        vector<string> ans;
        int n = s.length();
        if (n <= L) 
            return ans;
        int x = 0;
        for (int i = 0; i < L - 1; i++) {
            x = (x << 2) | bin[s[i]];// | 逻辑与 bin为2进制文件
        }
        unordered_map<int, int> cnt;
        for (int i = 0; i <= n - L; i++) {
            x = ((x << 2) | bin[s[i + L - 1]]) & ((1 << (L * 2)) - 1);
            if (++cnt[x] == 2) {
                ans.push_back(s.substr(i, L));
            }
        }
        return ans;
    }
};
```

