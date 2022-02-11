# Leetcode No.407

###### [407. 接雨水 II](https://leetcode-cn.com/problems/trapping-rain-water-ii/)

###### [**42.接雨水**](https://leetcode-cn.com/problems/trapping-rain-water/)

方法一：最小堆（小根堆）

本题目相较于42，变化就是从一维数组转变成为二位数组，那么接水的方式就变成了上下左右四个相邻的方块接水后的高度都要低。

这里设置方块坐标为(i，j)，方块的高度为 heightMap\[i]\[j]，接到水后的高度为water\[i]\[j]，那么节水后高度为：
$$
water[i][j]=max(height[i],min(water[i-1][j],water[i+1][j],water[i][j+1],water[i][j-1])
$$
实际接水后的高度就是方块自身的高度，因为最外层的方块无法接水，所以最外层方块water\[i][j] = heightMap\[i]\[j]。

我们假设已经知道最外层的方块接水后的高度的最小值，则此时我们根据木桶原理，肯定可以确定最小高度方块的相邻方块的接水高度。我们同时更新最外层的方块标记，我们在新的最外层的方块再次找到接水后的高度的最小值，同时确定与其相邻的方块的接水高度

```c++
typedef pair<int,int> pii;

class Solution {
public:
    int trapRainWater(vector<vector<int>>& heightMap) {  
        if (heightMap.size() <= 2 || heightMap[0].size() <= 2) {
            return 0;
        }  
        int m = heightMap.size();
        int n = heightMap[0].size();
        priority_queue<pii, vector<pii>, greater<pii>> pq;
        vector<vector<bool>> visit(m, vector<bool>(n, false));
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i == 0 || i == m - 1 || j == 0 || j == n - 1) {
                    pq.push({heightMap[i][j], i * n + j});
                    visit[i][j] = true;
                }
            }
        }

        int res = 0;
        int dirs[] = {-1, 0, 1, 0, -1};
        while (!pq.empty()) {
            pii curr = pq.top();
            pq.pop();            
            for (int k = 0; k < 4; ++k) {
                int nx = curr.second / n + dirs[k];
                int ny = curr.second % n + dirs[k + 1];
                if( nx >= 0 && nx < m && ny >= 0 && ny < n && !visit[nx][ny]) {
                    if (heightMap[nx][ny] < curr.first) {
                        res += curr.first - heightMap[nx][ny]; 
                    }
                    visit[nx][ny] = true;
                    pq.push({max(heightMap[nx][ny], curr.first), nx * n + ny});
                }
            }
        }
        
        return res;
    }
};
```

