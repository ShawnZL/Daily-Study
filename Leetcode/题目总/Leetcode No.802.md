# Leetcode No.802

#### [802. 找到最终的安全状态](https://leetcode-cn.com/problems/find-eventual-safe-states/)

这道题本来使用DFS进行遍历，找到终点，从终点向前遍历。但是忽略题目条件**无论每一步选择沿哪条有向边行走，后必然在有限步内到达终点**

方法一：拓扑排序

反向拓扑，把所有边反向

```c++
class Solution {
public:
    vector<int> eventualSafeNodes(vector<vector<int>> &graph) {
        int n = graph.size();
        vector<vector<int>> rg(n);
        vector<int> inDeg(n);
        for (int x = 0; x < n; ++x) {
            for (int y : graph[x]) {
                rg[y].push_back(x);
            }
            inDeg[x] = graph[x].size();
        }

        queue<int> q;
        for (int i = 0; i < n; ++i) {
            if (inDeg[i] == 0) {
                q.push(i);
            }
        }
        while (!q.empty()) {
            int y = q.front();
            q.pop();
            for (int x : rg[y]) {
                if (--inDeg[x] == 0) {
                    q.push(x);
                }
            }
        }

        vector<int> ans;
        for (int i = 0; i < n; ++i) {
            if (inDeg[i] == 0) {
                ans.push_back(i);
            }
        }
        return ans;
    }
};
```

BFS + 三色标记法

若起始节点位于一个环里，或者可以到达环就是不安全。

白色0：表示尚未被访问

灰色1：表示位于递归栈中

黑色2：表示搜索完成是一个安全节点，

起始节点标记为灰色，在遍历过程遇到一个灰色节点，说明找到了一个环，退出搜索。

```c++
class Solution {
public:
    vector<int> eventualSafeNodes(vector<vector<int>> &graph) {
        int n = graph.size();
        vector<int> color(n);

        function<bool(int)> safe = [&](int x) {
            if (color[x] > 0) {
                return color[x] == 2;
            }
            color[x] = 1;
            for (int y : graph[x]) {
                if (!safe(y)) {
                    return false;
                }
            }
            color[x] = 2;
            return true;
        };

        vector<int> ans;
        for (int i = 0; i < n; ++i) {
            if (safe(i)) {
                ans.push_back(i);
            }
        }
        return ans;
    }
};
```

