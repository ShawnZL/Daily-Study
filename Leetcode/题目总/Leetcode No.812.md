# Leetcode No.812

#### [812. 最大三角形面积](https://leetcode.cn/problems/largest-triangle-area/)

![](https://pic.leetcode-cn.com/1652581790-XRkRMK-812.%20%E6%9C%80%E5%A4%A7%E4%B8%89%E8%A7%92%E5%BD%A2%E9%9D%A2%E7%A7%AF.002.png)

```c++
double largestTriangleArea(vector<vector<int>>& points) {
        int lens = points.size();
        double res = 0;
        for (int x = 0; x < lens - 2; ++x) {
            for (int y = x + 1; y < lens - 1; ++y) {
                for (int z = x + 2; z < lens; ++z) {
                    auto& pointsx = points[x];
                    auto& pointsy = points[y];
                    auto& pointsz = points[z];
                    int x1 = pointsx[0], y1 = pointsx[1];
                    int x2 = pointsy[0], y2 = pointsy[1];
                    int x3 = pointsz[0], y3 = pointsz[1];
                    res = max(res, 0.5 * abs(x1 * (y2 - y3) + x2 * (y3 - y1) + x3 * (y1 - y2)));
                }
            }
        }
        return res;
    }
```



```python
class Solution:
    def largestTriangleArea(self, points: List[List[int]]) -> float:
        res = 0.0
        lens = len(points)
        for i in range(lens - 2):
            for j in range(i + 1, lens - 1):
                for k in range(i + 2, lens):
                    [x1,y1],[x2,y2],[x3,y3] = points[i], points[j], points[k]
                    res = max(res, 0.5 * abs(x1 * (y2 - y3) + x2 * (y3 - y1) + x3 * (y1 - y2)))
        return res
```

