# Leetcode No.1713

[得到子序列的最少操作数](https://leetcode-cn.com/problems/minimum-operations-to-make-a-subsequence/solution/de-dao-zi-xu-lie-de-zui-shao-cao-zuo-ci-hefgl/)

本体十分难理解

根据题意，target 和 arr 这两个数组的公共子序列越长，需要添加的元素个数也就越少。因此最少添加的元素个数为 n，n 减去两数组的最长公共子序列的长度。

由于 target的元素互不相同，我们可以用一个哈希表记录 target的每个元素所处的下标，并将 arr 中的元素映射到下标上，对于不存在于 target中的元素，由于其必然不会在最长公共子序列中，可将其忽略。

```
例：
target = [6,4,8,1,3,2]
arr = [4,7,6,2,3,8,6,1]

原问题 ==》求：target和arr的最长公共子序列长度LCS
最少操作次数 = target.length- lcs
时间复杂度：O(n*m)，target.length, arr.length <= 10^5，无法通过

由于target中的元素不重复，可将两个数组转成对应target的下标数组：
target': 0,1,2,3,4,5
arr': 1,0,5,4,2,0,3
问题 ==》求：target'和arr'的最长公共子序列长度LCS

由于target'是严格单调递增的
问题 ==》求：arr'的最长递增子序列的长度LIS
```

```c++
class Solution {
public:
    int minOperations(vector<int>& target, vector<int>& arr) {
        int n = target.size();
        unordered_map<int, int> pos;
        for (int i = 0; i < n; i++) {
            pos[target[i]] = i;
        }
        vector<int> d;
        for (int val: arr) {
            if (pos.count(val)) {
                int idx = pos[val];
                auto it = lower_bound(d.begin(), d.end(), idx);
                if (it != d.end()) {
                    *it = idx;
                } else {
                    d.push_back(idx);
                    printf("%d=%d\n", val, idx);
                }
            }
        }
        return n - d.size();
    }
};
```

