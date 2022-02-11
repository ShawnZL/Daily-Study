# Leetcode No.1137

[第N个泰波纳契数](https://leetcode-cn.com/problems/n-th-tribonacci-number/)

使用空间换时间的做法

```c++
class Solution {
public:
    int tribonacci(int n) {
        vector<int> ans;
        ans.push_back(0);
        ans.push_back(1);
        ans.push_back(1);
        for (int i = 3; i <= n; i++) {
            ans.push_back(ans[i-1] + ans[i-2] + ans[i-3]);
        }
        return ans[n];
    }
};
```

