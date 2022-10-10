# Leetcode No.942

#### [942. 增减字符串匹配](https://leetcode.cn/problems/di-string-match/)

```c++
class Solution {
public:
    vector<int> diStringMatch(string s) {
        int maxlen = s.size();
        int left_index = 0, right_index = maxlen;
        vector<int> res;
        for (int i = 0; i < maxlen; ++i) {
            if (s[i] == 'I') 
                res.push_back(left_index++);
            else
                res.push_back(right_index--);
        }
        res.push_back(left_index);
        return res;
    }
};

class Solution {
public:
    vector<int> diStringMatch(string s) {
        int maxlen = s.size();
        int left_index = 0, right_index = maxlen;
        vector<int> res(maxlen + 1);
        for (int i = 0; i < maxlen; ++i) {
            res[i] = s[i] == 'I' ? left_index++ : right_index--;
        }
        res[maxlen] = left_index;
        return res;
    }
};
```



```python
class Solution:
    def diStringMatch(self, s: str) -> List[int]:
        right = Maxlen = len(s)
        left = 0 
        res = [0] * (Maxlen + 1)
        for i, ch in enumerate(s):
            if ch == 'I':
                res[i] = left;
                left += 1;
            else:
                res[i] = right;
                right -= 1;
        res[Maxlen] = left;
        return res

```

其中 enumerate:

enumerate() 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中

```
enumerate(sequence, [start=0])

>>> seasons = ['Spring', 'Summer', 'Fall', 'Winter']
>>> list(enumerate(seasons))
[(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
>>> list(enumerate(seasons, start=1))       # 下标从 1 开始
[(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```

