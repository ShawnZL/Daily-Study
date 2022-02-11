# Leetcode No.120

###### [120. 三角形最小路径和](https://leetcode-cn.com/problems/triangle/)

动态规划

从下到上，相较于931，931可以使用从上到下算，但是本体从下往上算简单，因为下大上小，所以越往上计算越小！！！

```c++
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        int len = triangle.size();
        for (int i = len - 2; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                triangle[i][j] += min(triangle[i + 1][j], triangle[i + 1][j + 1]);
            }
        }
        return triangle[0][0];
    }
};
```

