# Leetcode No.668

#### [668. 乘法表中第k小的数](https://leetcode.cn/problems/kth-smallest-number-in-multiplication-table/)

[解析](https://leetcode.cn/problems/kth-smallest-number-in-multiplication-table/solution/by-fuxuemingzhu-8eq4/)

```
class Solution {
public:
    int findKthNumber(int m, int n, int k) {
        int left = 1;
        int right = m * n;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (count(m,n,mid) >= k)
                right = mid;
            else
                left = mid + 1;
        }
        return left;
    }
    int count(int m, int n, int k) {
        int res = 0;
        // 统计每行小于等于 k 的数目
        for (int i = 1; i <= m; ++i) {
            res += min(k/i, n);
        }
        return res;
    }
};
```

