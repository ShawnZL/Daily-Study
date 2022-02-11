# Leetcode No.392

###### [392. 判断子序列](https://leetcode-cn.com/problems/is-subsequence/)

###### 方法一：直接暴力配对

```c++
class Solution {
public:
    bool isSubsequence(string s, string t) {
        int n1 = s.length();
        int n2 = t.length();
        int flag1 = 0, flag2 = 0;
        while (flag1 < n1 && flag2 < n2) {
            while(flag2 < n2 && t[flag2] != s[flag1]) {
                flag2++;
            }
            flag2++;
            flag1++;
            //printf("%d %d\n", flag1, flag2);
        }
        if (flag1 == n1 && flag2 <= n2) return true;
        return false;
    }
};
```

###### 方法二：动态规划算法

```c++
class Solution {
public:
    bool isSubsequence(string s, string t) {
        int n = s.size(), m = t.size();

        vector<vector<int> > f(m + 1, vector<int>(26, 0));
        for (int i = 0; i < 26; i++) {
            f[m][i] = m;
        }

        for (int i = m - 1; i >= 0; i--) {
            for (int j = 0; j < 26; j++) {
                if (t[i] == j + 'a')
                    f[i][j] = i;
                else
                    f[i][j] = f[i + 1][j];
            }
        }
        int add = 0;
        for (int i = 0; i < n; i++) {
            if (f[add][s[i] - 'a'] == m) {
                return false;
            }
            add = f[add][s[i] - 'a'] + 1;
        }
        return true;
    }
};

```

