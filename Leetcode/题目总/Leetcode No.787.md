# Leetcode No.787

###### [787. K 站中转内最便宜的航班](https://leetcode-cn.com/problems/cheapest-flights-within-k-stops/)

方法一：BFS+路径记忆

```c++
class Solution {
public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        queue<pair<int, int>> Q;
        Q.push(make_pair(src, 0));
        
        unordered_map<int, unordered_map<int, int>> gragh;
        vector<int> dis(n, INT_MAX);

        for (auto flight : flights) {
            gragh[flight[0]][flight[1]] = flight[2];
        }
        int step = 0;
        int ans = -1;
        dis[src] = 0;

        while(!Q.empty() && step <= k + 1) {
            int size = Q.size();
            while(size) {
                auto[node, distance] = Q.front();

                Q.pop();
                size--;
                if (node == dst && (ans == -1 || ans > distance)) {
                    ans = distance;
                    continue;
                }

                for (auto [next, weight] : gragh[node]) {
                    if (distance + weight < dis[next]) {
                        dis[next] = distance + weight;
                        Q.push(make_pair(next, dis[next]));
                    }
                }
            }
            step++;
        }
        return ans;

    }
};
```

方法二：动态规划

dp\[step][distance] 表示到达点distance需要step步

边界条件dp\[0][src] = 0 dp\[t][i] = 无穷
$$
dp[i][y] = min(dp[i - 1][x] + cost[x, y])
$$


```c++
class Solution {
private:
    static constexpr int INF = 10000 * 101 + 1;

public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        vector<vector<int>> dp(k + 2, vector<int>(n, INF));
        //k个中转加出发和终点
        dp[0][src] = 0;
        for (int i = 1; i <= k + 1; i++) {
            for (auto && flight: flights) {
                int x = flight[0], y = flight[1], cost = flight[2];
                dp[i][y] = min(dp[i][y], dp[i - 1][x] + cost);
            }
        }
        int ans = INF;
        for (int i = 1; i <= k + 1; i++) {
            ans = min(ans, dp[i][dst]);
        }
        return (ans == INF ? -1 : ans);
    }
};
```

