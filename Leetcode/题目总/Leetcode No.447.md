# Leetcode No.447

###### [447. 回旋镖的数量](https://leetcode-cn.com/problems/number-of-boomerangs/)

方法一：我们选择其中一个作为V型曲线的折线，遍历每一个节点，使其成为端点顶点，设置points中有m个点到points[i]的距离均相等，从中选择两个作为端点，组合排列数就是A2m  = m * (m - 1)

```c++
class Solution {
public:
    int numberOfBoomerangs(vector<vector<int>> &points) {
        int ans = 0;
        for (auto &p : points) {
            unordered_map<int, int> cnt;
            for (auto &q : points) {
                int dis = (p[0] - q[0]) * (p[0] - q[0]) + (p[1] - q[1]) * (p[1] - q[1]);
                ++cnt[dis];
            }
            for (auto &[_, m] : cnt) {
                //表示[i, m个]
                ans += m * (m - 1);
            }
        }
        return ans;
    }
};

```

```c++
class Solution {
public:
    int numberOfBoomerangs(vector<vector<int>>& points) {
        int ans = 0;
        unordered_map<int, int> cnt;
        for (auto &x : points) {
            cnt.clear();
            for (auto &y : points) {
                ans += 2 * cnt[(x[0] - y[0]) * (x[0] - y[0]) + (x[1] - y[1]) * (x[1] - y[1])]++;
            }
        }
        return ans;
    }
};
```

