# Leetcode No.74

###### [74. 搜索二维矩阵](https://leetcode-cn.com/problems/search-a-2d-matrix/)

方法一：根据最后一位是最大的数来确认数字在具体那一行，最大值没有满足时，直接输出false，接下来就是判定在目标行中，有那一个满足情况。

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();
        int flag = m;
        for (int i = 0; i < m; i++) {
                if (matrix[i][n - 1] >= target) {
                    flag = i;
                    break;
                }
        }
        if (flag == m) return false;
        for (int i = 0; i < n; i++) {
            if (matrix[flag][i] == target)
                return true;
        }
        return false;
    }
};
```

