# Leetcode NO.1743

[从相邻元素对还原数组](https://leetcode-cn.com/problems/restore-the-array-from-adjacent-pairs/)

画图发现本题目中，首尾的元素在元组对中只出现一次，所以应该先确认首尾元素，接下来从首尾遍历即可

方法一：使用哈希表 完成DFS

```c++
class Solution {
public:
    vector<int> restoreArray(vector<vector<int>>& adjacentPairs) {
       unordered_map<int, vector<int>> mp;
       for (auto& adjacentPair : adjacentPairs) {
           mp[adjacentPair[0]].push_back(adjacentPair[1]);
           mp[adjacentPair[1]].push_back(adjacentPair[0]);
       }

       int n = adjacentPairs.size() + 1;//为啥+1?
       vector<int> ret(n);
       for (auto& [e, adj] : mp) {
           if(adj.size() == 1) {
               ret[0] = e;
               break;
           }
       }

       ret[1] = mp[ret[0]][0];
       for (int i = 2; i < n; i++) {
           auto& adj = mp[ret[i - 1]];
           ret[i] = ret[i - 2] == adj[0] ? adj[1] : adj[0];//同过比较是否是上一个元素，确定下一个相连接的元素
       }
       return ret;
    }
};
```

方法二：BFS，可以使用BFS是因为在排除相连的一个连接后，就只有一个连接点，可以变为DFS

```c++
class Solution {
public:
    vector<int> restoreArray(vector<vector<int>>& adjacentPairs) {
        unordered_map<int, int> degree;
        unordered_map<int, vector<int>> mp;
        for (auto& a: adjacentPairs) {
            degree[a[0]]++;
            degree[a[1]]++;
            mp[a[0]].push_back(a[1]);
            mp[a[1]].push_back(a[0]); 
        }

        unordered_map<int, int> vis;
        int st = 0;
        for (auto& [u, ct] : degree) 
            if (ct & 1) {
                //ct & 1 <==> ct == 1
                st = u;
                printf("%d\n", u);
                break;
            }
        vector<int> ans;
        queue<int> q;
        q.push(st);
        while(!q.empty()) {
            auto u = q.front();
            q.pop();
            vis[u] = 1;
            ans.push_back(u);
            for(auto& v: mp[u])
                if(!vis[v])
                    q.push(v);
        }
        return ans;
    }
};
```

