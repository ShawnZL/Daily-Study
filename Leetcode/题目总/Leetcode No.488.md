# Leetcode No.488

###### [488. 祖玛游戏](https://leetcode-cn.com/problems/zuma-game/)

方法一：深度优先遍历 + 剪枝

考虑字符串 hand 中每一个字符可能放置的位置，枚举 hand 中每一个字符出现在字符串 board 的位置，添加后要移出字符串 board 中出现 三个或者三个以上 且 颜色相同 的球，同时对已经搜过的状态进行记忆化，也就是存起来。

以上代码实现分为两部分：

如何删除字符串 board 中出现 三个或者三个以上 且 颜色相同的球：这是一个经典的双指针问题，枚举两个端点 i, j，一开始 i = j，找到 board[i] != board[j] 的第一个位置，即区间 [i,j−1] 就是 board 连续且相同的字符区间，如果区间长度大于3，就将其删除。
如何枚举字符串 hand 中每一个字符可能放置在字符串 board 的位置，第一层 for 枚举字符串hand 的字符，第二次for枚举字符串 board 的位置，将字符串 hand 的字符添加到符串 board 对应的位置中。

```c++
class Solution {
public:
    unordered_map<char, int> mp;
    unordered_map<string, bool> vis;
    char color[5] = {'R', 'Y', 'B', 'G', 'W'};
    int ans = 100;

    string eliminate(string s) {
        bool flag = true;
        while (flag) {
            flag = false;
            for (int i = 0, j = 0; i < s.size(); i = j) {
                while (j < s.size() && s[i] == s[j])
                    ++j;
                if (j - i >= 3) {
                    s = s.substr(0, i) + s.substr(j);
                    flag = true;
                    break;
                }
            }
        }
        return s;
    }

    void dfs(string s, int step) {
        if (step >= ans) 
            return;
        if (s.size() == 0) {
            ans = min(ans, step);
            return;
        }
        if (vis[s]) 
            return;
        vis[s] = true;

        for (int i = 0; i < 5; ++i) {
            if (!mp[color[i]])
                continue;
            for (int j = 0; j < s.size(); ++j) {
                string next = s.substr(0, j + 1) + color[i] + s.substr(j + 1);
                next = eliminate(next);
                mp[color[i]] -= 1;
                dfs(next, step + 1);
                mp[color[i]] += 1;
            }
        }
    }

    int findMinStep(string board, string hand) {
        for (auto &it: hand) 
            ++mp[it];
        board = eliminate(board);
        dfs(board, 0);
        return ans == 100 ? -1 : ans;
    }

};
```

