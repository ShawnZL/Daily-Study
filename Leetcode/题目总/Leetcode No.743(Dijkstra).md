# Leetcode No.743(Dijkstra)

[网络延迟时间](https://leetcode-cn.com/problems/network-delay-time/submissions/)

本道题利用邻接表和BFS进行编辑，**官方做法就是Dijkstra算法。**

**但是遇到问题就是一个最短距离跟新完毕以后，我再次将这个跟新后的节点放进来队列中，以防止有的节点a距离已经计算完毕，但是在a最短距离路上有b是在a之后迭代完成最短距离跟新，造成a的距离并不是最小，所以将b再次送回队列。每一次跟新了最短距离，我就将这个节点再次送回队列，让结果再次迭代。**

```c++
class Solution {
public:
    //BFS
    int networkDelayTime(vector<vector<int>>& times, int n, int k) {
        //vector<vector<int>> nodes(n+1, vector<int>(n+1， -1));//构建一个邻接矩阵
        vector<vector<int>> nodes(n+1);
        vector<vector<int>> dis(n+1);
        queue<int> res;//遍历队列
        vector<bool> reading(n+1, false);
        vector<int> ans(n+1, -1);//记录到达每一个节点时间
        /*for (int i = 0; i < n; i++) {
            reading[i] = false;//初始化
            ans[i] = -1;
        }*/
        int len = times.size(), maxn = -1;
        for (int i = 0; i < len; i++) {
            //nodes[times[i][0]][times[i][1]] = times[i][2];//邻接矩阵录入距离
            nodes[times[i][0]].push_back(times[i][1]);
            dis[times[i][0]].push_back(times[i][2]);
        }
        //遍历过程  
        reading[k] = true;
        res.push(k);
        ans[k] = 0;
        while(!res.empty()) {
            int start = res.front();//出队
            res.pop();
            for (int i = 0; i < nodes[start].size(); i++) {
                //nodes[start][i]表示当前访问的节点
                if (!reading[nodes[start][i]]) {
                    //第一次遍历节点
                    ans[nodes[start][i]] = ans[start] + dis[start][i];
                    //printf("%d %d dis=%d\n", start, nodes[start][i], ans[nodes[start][i]]);
                    reading[nodes[start][i]] = true;//表示访问过
                    res.push(nodes[start][i]);//入队
                }
                else {//读过的节点一律保存最小值
                    //printf("tes:%d %d start=%d dis=%d\n", start, nodes[start][i], ans[start]+dis[start][i], ans[nodes[start][i]]);       if (nodes[start][i] != k) {
                        if (ans[start] + dis[start][i] < ans[nodes[start][i]]) {
                            ans[nodes[start][i]] = ans[start] + dis[start][i];
                            res.push(nodes[start][i]);//此时更改数据，再次遍历
                        }
                        //ans[nodes[start][i]] = min(ans[start] + dis[start][i], ans[nodes[start][i]]);
                        //printf("%d %d dis=%d\n", start, nodes[start][i], ans[nodes[start][i]]);
                }
            }
        }
        
        for (int i = 1; i <= n; i++) {
            //遍历中只要有一个是-1，就是没有遍历到
            if (ans[i] == -1) {
                maxn = -1;break;
            } 
            maxn = max(maxn, ans[i]);
        }
        if (maxn) return maxn;
        return -1;
    }
};
```



```c++
class Solution {
public:
    int networkDelayTime(vector<vector<int>>& times, int n, int k) {
        const int inf = INT_MAX / 2;
        vector<vector<int>> g(n, vector<int>(n, inf));
        for (auto &t: times) {
            int x = t[0] - 1, y = t[1] - 1;
            g[x][y] = t[2];
        }

        vector<int> dis(n, inf);
        dis[k - 1] = 0;
        vector<int> used(n);
        for (int i = 0; i < n; i++) {
            int x = -1;
            for (int y = 0; y < n; y++) {
                if (!used[y] && (x == -1 || dis[y] < dis[x])) {
                    x = y;
                }
            }
            //寻找到当前可以求出距离最小的节点，
            //有新的中间节点后，跟新最小距离
            used[x] = true;
            for (int y = 0; y < n; y++) {
                dis[y] = min(dis[y], dis[x] + g[x][y]);
            }
        }
        int ans = *max_element(dis.begin(), dis.end());
        return ans == inf ? -1 : ans;
    }
};
```

