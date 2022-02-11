# Leetcode No.1314

###### [1314. 矩阵区域和](https://leetcode-cn.com/problems/matrix-block-sum/)

使用P表示和，维度(m + 1, n + 1)，如果我们还设置为(m, n)，那么就会有一个问题，我们对于起始角元素无法相加,
$$
sum = P[i + K + 1][j + K + 1] - P[i - K][j + K + 1] - P[i + K + 1][j - K] + P[i - K][j - K]
$$
i + k + 1, j + k - 1， i - k, j - k这些下标可能越界

更一般的做法是，我们对所有的横坐标与 `m` 取较小值，纵坐标与 `n` 取较小值，再将所有坐标与 `0` 取较大值，就可以将这些坐标规范在前缀和数组 `P` 的范围内。

```c++
class Solution {
public:
    int get(const vector<vector<int>>& pre, int m, int n, int x, int y) {
        x = max(min(x, m), 0);
        y = max(min(y, n), 0);
        return pre[x][y];
    }
    
    vector<vector<int>> matrixBlockSum(vector<vector<int>>& mat, int K) {
        int m = mat.size(), n = mat[0].size();
        vector<vector<int>> P(m + 1, vector<int>(n + 1));
        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                P[i][j] = P[i - 1][j] + P[i][j - 1] - P[i - 1][j - 1] + mat[i - 1][j - 1];
            }
        }
        
        vector<vector<int>> ans(m, vector<int>(n));
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                ans[i][j] = get(P, m, n, i + K + 1, j + K + 1) - get(P, m, n, i - K, j + K + 1) - get(P, m, n, i + K + 1, j - K) + get(P, m, n, i - K, j - K);
            }
        }
        return ans;
    }
};
```

