# Leetcode No.221

###### [221. 最大正方形](https://leetcode-cn.com/problems/maximal-square/)

使用动态规划，我们使用dp\[i][j]表示以(i, j)为右下角，且只包含1的正方形的边长最大值，同时记录最大的dp\[i][j]为maxSide，

若形成正方形（非单 `1`），以当前为右下角的视角看，则需要：当前格、上、左、左上都是 `1`，可以换个角度：当前格、上、左、左上都不能受 `0` 的限制，才能成为正方形，[解析链接](https://leetcode-cn.com/problems/maximal-square/solution/li-jie-san-zhe-qu-zui-xiao-1-by-lzhlyle/)

dp\[i][j] = 0，因为当前位置不可能在由1组成的正方形中，

dp\[i][j] = 1，则当前位置由其dp\[i - 1][j]，dp\[i][j - 1]，dp\[i - 1][j - 1]

做法原理是我们将(i, j)为右下角，那么左侧(i，j - 1)，上方(i - 1, j)，左上方(i - 1，j - 1),均可以作为正方形的一角，[链接](https://leetcode-cn.com/problems/count-square-submatrices-with-all-ones/solution/tong-ji-quan-wei-1-de-zheng-fang-xing-zi-ju-zhen-2/)，将三个不等式联立，
$$
dp[i][j] = min(dp[i -1][j], dp[i][j-1],dp[i - 1][j - 1])
$$
例子：

```
0 1 1 1 0        0 1 1 1 0
1 1 1 1 0        1 1 2 2 0
0 1 1 1 1        0 1 2 3 1
0 1 1 1 1        0 1 2 3 2
0 0 1 1 1        0 0 1 2 3
```



```c++
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        if (matrix.size() == 0 || matrix[0].size() == 0) {
            return 0;
        }
        int maxSide = 0;
        int rows = matrix.size(), columns = matrix[0].size();
        vector<vector<int>> dp(rows, vector<int>(columns));
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {
                if (matrix[i][j] == '1') {
                    if (i == 0 || j == 0) {
                        dp[i][j] = 1;
                    } else {
                        dp[i][j] = min(min(dp[i - 1][j], dp[i][j - 1]), dp[i - 1][j - 1]) + 1;
                    }
                    maxSide = max(maxSide, dp[i][j]);
                }
            }
        }
        int maxSquare = maxSide * maxSide;
        return maxSquare;
    }
};
```

