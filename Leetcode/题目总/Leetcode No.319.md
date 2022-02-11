# Leetcode No.319

###### [319. 灯泡开关](https://leetcode-cn.com/problems/bulb-switcher/)

方法一：直接模拟运行过程

```c++
class Solution {
public:
    int bulbSwitch(int n) {
        if (n == 1 || n == 0) return n;
        int res = 0;
        vector<bool> bul(n + 1, true);
        for (int i = 2; i <= n; ++i) {
            for (int j = i; j <= n; j += i)
                bul[j] = !bul[j];
        }
        for (int i = 1; i <= n; i++) {
            if (bul[i])
                ++res;
        }
        return res;
    }
};
```

**但是运行时间超时！**

方法二：

发现第 i 轮，编号为 i 的倍数灯泡进行切换

因此对于第 k 个灯泡，他被切换的次数就是k 的约数个数。如果 k 有偶数个约数，那么 第 k 个灯泡最终状态为暗；如果 k 有奇数个约数，那么最终第 k 个灯泡状态为亮。

对于 k 而言，如果它有约数 x，那么一定有约数 k/x。因此只有当 x ^ 2 != k 时，约数都是「成对」出现的。这就说明，只有当 k 是「完全平方数」时，它才会有奇数个约数，否则一定有偶数个约数。

所有我们只需要找到1-n中的完全平方数个数即可。就是

```
class Solution {
public:
    int bulbSwitch(int n) {
        return sqrt(n + 0.5);
    }
};
```

