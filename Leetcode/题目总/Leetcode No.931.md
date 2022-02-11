# Leetcode No.931

###### [931. 下降路径最小和](https://leetcode-cn.com/problems/minimum-falling-path-sum/)

动态规划

从下到上

```c++
class Solution {
public:
    int minFallingPathSum(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        for (int i = m - 2; i >= 0; i--) {
            for (int j = 0; j < n; j++) {
                int best = matrix[i + 1][j];
                if (j > 0) {
                    best = min(best, matrix[i + 1][j - 1]);
                }
                if (j + 1 < n) 
                    best = min(best, matrix[i + 1][j + 1]);
                matrix[i][j] += best;
            }
        }
        int ans = INT_MAX;
        for (int j : matrix[0]) {
            ans = min(ans, j);
        }
        return ans;
    }
};
```

从上到下

```c++
class Solution {
public:
    int minFallingPathSum(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        for (int i = 1; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int best = matrix[i - 1][j];
                if (j > 0) {
                    best = min(best, matrix[i - 1][j - 1]);
                }
                if (j + 1 < n) 
                    best = min(best, matrix[i - 1][j + 1]);
                matrix[i][j] += best;
            }
        }
        int ans = INT_MAX;
        for (int j : matrix[m - 1]) {
            ans = min(ans, j);
        }
        return ans;
    }
};
```

