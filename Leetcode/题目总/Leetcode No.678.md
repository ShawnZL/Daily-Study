# Leetcode No.678

###### [678. 有效的括号字符串](https://leetcode-cn.com/problems/valid-parenthesis-string/)

本人方法是使用Stack，我利用count记录*的数量，每一次遍历到 ‘(’ 入栈，如果遍历到 ‘\*’ 直接count加 1，对于遍历到 ’\)‘ ，首先查看是否存在栈中又可以匹配的左括号，如果没有再去利用 * 作为一个左括号匹配，到遍历完成，我们利用 * 去作为右括号，但是这个方法存在一个问题，如果我们面对这个 "\*\*((\*(((((****\" 此时的 \* 已经无法变换为任何一个括号，所以我们改进算法，多建立一个记录 * 位置的栈， 同时我们向栈中压入的为符号位置

```c++
class Solution {
public:
    bool checkValidString(string s) {
        stack<char> Stk;
        int len = s.length();
        int count = 0;
        if (len == 0) return true;
        for (int i = 0; i < len; i++) {
            if (s[i] == '(') {
                Stk.push('(');
            }
            else if (s[i] == '*') {
                count++;
            }
            else {
                if (!Stk.empty())
                    Stk.pop();
                else if (count > 0)
                    count--;
                else
                    return false;
            }
            printf("%d ", count);
        }
        while (!Stk.empty()) {
            Stk.pop();
            count--;
        }
        if (Stk.empty() && count >= 0) 
            return true;
        else
            return false;
    }
};
```

完成代码

```c++
class Solution {
public:
    bool checkValidString(string s) {
        stack<int> Stk1;//记录左括号位置
        stack<int> Stk2;//记录*号位置
        int len = s.length();
        if (len == 0) return true;
        for (int i = 0; i < len; i++) {
            if (s[i] == '(') {
                Stk1.push(i);
            }
            else if (s[i] == '*') {
                Stk2.push(i);
            }
            else {
                //存在左括号匹配
                if (!Stk1.empty())
                    Stk1.pop();
                //存在*匹配
                else if (!Stk2.empty())
                    Stk2.pop();
                else
                    return false;
            }
        }
        while (!Stk1.empty() && !Stk2.empty()) {
            if (Stk1.top() < Stk2.top()) {
                Stk1.pop();
                Stk2.pop();
            }
            else 
                return false;
        }

        return Stk1.empty();
    }
};
```

方法二：动态规划

使用dp\[i][j] 表示字符串s从下标i 到 j 的子串是否为有效的括号字符串，其中 0 <= i <= j <= n

当子串长度为1，只有 * 才是有效的字符串

当子串长度为2，只有 “()” “(\*” “\*)” “**” 才是有效的括号字符串

当大于2时，需要根据子串的首尾以及中间字符判断是否为有效的字符串

s[i] s[j] 分别为左右括号，或者为“\*” ,当dp\[i + 1][j - 1] = true, dp\[i][j] = true;

如果存在i <= k < j 使得 dp\[i]\[k] 和 dp\[k + 1]\[j] 都是true，则dp\[i]\[j] = true,

```c++
class Solution {
public:
    bool checkValidString(string s) {
        int n = s.size();
        vector<vector<bool>> dp = vector<vector<bool>>(n,vector<bool>(n,false));

        for (int i = 0; i < n; i++) {
            if (s[i] == '*') {
                dp[i][i] = true;
            }
        }

        for (int i = 1; i < n; i++) {
            char c1 = s[i - 1]; 
            char c2 = s[i];
            dp[i - 1][i] = (c1 == '(' || c1 == '*') && (c2 == ')' || c2 == '*');
        }

        for (int i = n - 3; i >= 0; i--) {
            char c1 = s[i];
            for (int j = i + 2; j < n; j++) {
                char c2 = s[j];
                if ((c1 == '(' || c1 == '*') && (c2 == ')' || c2 == '*')) {
                    dp[i][j] = dp[i + 1][j - 1];
                }
                for (int k = i; k < j && !dp[i][j]; k++) {
                    dp[i][j] = dp[i][k] && dp[k + 1][j];
                }
            }
        }
        return dp[0][n - 1];
    }
};

```

