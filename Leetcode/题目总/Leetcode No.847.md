# Leetcode No.847

###### [847. 访问所有节点的最短路径](https://leetcode-cn.com/problems/shortest-path-visiting-all-nodes/)

图的算法

方法一：相较于常规广度优先遍历，会在队列中存储节点的编号，对于本题目而言，使用三元组(u, mask, dist)表示队列中的每一个元素，u表示当前位于节点的编号；mask是一个长度为n的二进制数，表示每一个节点是否经过，如果 mask 的第 i 位是 1，则表示节点 i已经过，否则表示节点 i未经过；dist表示到当前为止经过的长度，在搜索过程中，如果当前三元组mask包含n个1，就是可以返回dist最为答案

为了保证广度优先搜索时间复杂度的正确性，即同一个节点 u 以及节点的经过情况 mask 只被搜索到一次，我们可以使用数组或者哈希表记录(u,mask) 是否已经被搜索过，防止无效的重复搜索。

```c++
class Solution {
public:
    int shortestPathLength(vector<vector<int>>& graph) {
        int n = graph.size();
        queue<tuple<int, int, int>> q;
        vector<vector<int>> seen(n, vector<int>(1 << n));
        //1 << n ,左移一位 是n^2;
        //mask相当于是一个二进制！！！！
        for (int i = 0; i < n; i++) {
            q.emplace(i, 1 << i, 0);
            seen[i][1 << i] = true;
        }

        int ans = 0;
        while (!q.empty()) {
            auto [u, mask, dist] = q.front();
            q.pop();
            if (mask == (1 << n) - 1) {
                ans = dist;
                break;
            }
            for (int v : graph[u]) {
                int mask_v = mask | (1 << v);
                // | 按位或运算符，按二进制位进行"或"运算
                if (!seen[v][mask_v]) {
                    q.emplace(v, mask_v, dist + 1);
                    seen[v][mask_v] = true;
                }
            }
        }
        return ans;
    }
};
```



方法二：

```c++
class Solution {
public:
    int shortestPathLength(vector<vector<int>>& graph) {
        int n = graph.size();
        vector<vector<int>> d(n, vector<int>(n, n + 1));
        for (int i = 0; i < n; ++i) {
            for (int j: graph[i]) {
                d[i][j] = 1;
            }
        }
        // 使用 floyd 算法预处理出所有点对之间的最短路径长度
        for (int k = 0; k < n; ++k) {
            for (int i = 0; i < n; ++i) {
                for (int j = 0; j < n; ++j) {
                    d[i][j] = min(d[i][j], d[i][k] + d[k][j]);
                }
            }
        }

        vector<vector<int>> f(n, vector<int>(1 << n, INT_MAX / 2));
        for (int mask = 1; mask < (1 << n); ++mask) {
            // 如果 mask 只包含一个 1，即 mask 是 2 的幂
            if ((mask & (mask - 1)) == 0) {
                int u = __builtin_ctz(mask);
                f[u][mask] = 0;
            }
            else {
                for (int u = 0; u < n; ++u) {
                    if (mask & (1 << u)) {
                        for (int v = 0; v < n; ++v) {
                            if ((mask & (1 << v)) && u != v) {
                                f[u][mask] = min(f[u][mask], f[v][mask ^ (1 << u)] + d[v][u]);
                            }
                        }
                    }
                }
            }
        }

        int ans = INT_MAX;
        for (int u = 0; u < n; ++u) {
            ans = min(ans, f[u][(1 << n) - 1]);
        }
        return ans;
    }
};
```

