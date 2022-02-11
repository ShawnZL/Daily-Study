# Leetcode No.797

###### [797. 所有可能的路径](https://leetcode-cn.com/problems/all-paths-from-source-to-target/)

DFS算法，就是典型的迭代计算

```c++
class Solution {
public:
    vector<vector<int>> ans;
    vector<int> temp;
    void DFS(int start, int end, vector<vector<int>>& graph) {
        temp.push_back(start);
        if (start == end) {
            ans.push_back(temp);
        }
        else {
            for(auto& node: graph[start]) {
                DFS(node, end, graph);
            }
        }
        temp.pop_back();
    }
    vector<vector<int>> allPathsSourceTarget(vector<vector<int>>& graph) {
        int n = graph.size();
        DFS(0, n - 1, graph);
        return ans;
    }
};
```

```c++
class Solution {
public:
    vector<vector<int>> ans;
    vector<int> temp;
    void DFS(int start, int end, vector<vector<int>>& graph) {
        if (start == end) {
            ans.push_back(temp);
            return;
        }
        else {
            for(auto& node: graph[start]) {
                temp.push_back(node);
                DFS(node, end, graph);
                temp.pop_back();
            }
        }
    }
    vector<vector<int>> allPathsSourceTarget(vector<vector<int>>& graph) {
        int n = graph.size();
        temp.push_back(0);
        DFS(0, n - 1, graph);
        return ans;
    }
};
```



BFS：BFS要记录当前前边的路径，重点是记录每一次遍历之前路径。

```c++
class Solution {
private:
    vector<vector<int>> ans;
public:
    vector<vector<int>> allPathsSourceTarget(vector<vector<int>>& graph) {
        queue<vector<int>> que;
        que.push({0});

        while (!que.empty()) {
            auto path = que.front();
            que.pop();

            auto cur = path.back();
            for (auto& next: graph[cur]) {
                path.push_back(next);

                if (next == (int)graph.size() - 1) {
                    ans.push_back(path);
                }
                else {
                    que.push(path);
                }

                path.pop_back();
            }
        }
        return ans;
    }
};
```

