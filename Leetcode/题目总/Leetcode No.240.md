# Leetcode No.240

###### [240. 搜索二维矩阵 II](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)

方法一：直接遍历法

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();
        for (int i = 0; i < m; i++) {
            for (auto &num : matrix[i]) {
                if (num == target)
                    return true;
            }
        }
        return false;
    }
};
```

方法二：因为每一行都是升序的，直接进行二分查找

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();
        for (const auto& row: matrix) {
            auto it = lower_bound(row.begin(), row.end(), target);
            if (it != row.end() && *it == target) {
                return true;
            }
        }
        return false;
    }
};
```

方法三：Z字形查找

我们可以从矩阵matrix的右上角(0, n - 1) 进行搜索。在每一步的搜索过程中，如果我们位于位置 (x, y)，那么我们希望在matrix 的左下角为左下角，以(x, y) 为右上角的矩阵中进行搜索，即行的范围为[x, m - 1]，列为[0, y]:

matrix[x, y] = target，说明完成搜索

matrix[x, y] > target，由于每一列的元素都是升序，所有位于第 y 列的元素都是严格大于 target 的，y - 1;

如果 matrix[x,y]<target，由于每一行的元素都是升序排列的，那么在当前的搜索矩阵中，所有位于第 x 行的元素都是严格小于 target 的，因此我们可以将它们全部忽略，即将 x 增加 1。

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();
        int x = 0, y = n - 1;
        while (x < m && y >= 0) {
            if (matrix[x][y] == target) {
                return true;
            }
            else if (matrix[x][y] > target) {
                y--;
            }
            else
                x++;
        }
        return false;
    }
};
```

