# Leetcode No.598

###### [598. 范围求和 II](https://leetcode-cn.com/problems/range-addition-ii/)

方法一：直接保存所有加一步骤中，都在加一中的行和列

每一次迭代取保存最小的行和最小的列

```c++
class Solution {
public:
    int maxCount(int m, int n, vector<vector<int>>& ops) {
        int len = ops.size(), res = 0, minrow = INT_MAX, mincol = INT_MAX;
        if (ops.size() == 0) return m * n;
        for (auto op: ops) {
            minrow = min(minrow, op[0]);
            mincol = min(mincol, op[1]);
        }
        return minrow * mincol;
    }
};
```

```python
class Solution:
    def maxCount(self, m: int, n: int, ops: List[List[int]]) -> int:
        mincol = 500000
        minrow = 500000
        if (len(ops) == 0):
            return m * n
        for lis in ops:
            mincol = min(lis[1], mincol)
            minrow = min(lis[0], minrow)
        return mincol * minrow
```

