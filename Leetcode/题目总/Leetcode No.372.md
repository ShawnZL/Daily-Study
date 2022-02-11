# Leetcode No.372

###### [372. 超级次方](https://leetcode-cn.com/problems/super-pow/)

[讲解链接](https://leetcode-cn.com/problems/super-pow/solution/chao-ji-ci-fang-by-leetcode-solution-ow8j/)

掌握快速幂这一算法，具体可以见「[50. Pow(x, n) 的官方题解](https://leetcode-cn.com/problems/powx-n/solution/powx-n-by-leetcode-solution/)」。

方法一：倒序遍历

设 *a* 的幂次为 *n*。根据题意，*n* 从最高位到最低位的所有数位构成了数组 *b*。记数组 *b* 的长度为 *m*，有

```
class Solution {
    const int MOD = 1337;
    int pow(int x, int n) {
        int res = 1;
        while (n) {
            if (n % 2) {
                res = (long) res * x % MOD;
            }
            x = (long) x * x % MOD;
            n /= 2;
        }
        return res;
    }

public:
    int superPow(int a, vector<int>& b) {
        int ans = 1;
        for (int i = b.size() - 1; i >= 0; --i) {
            ans = (long) ans * pow(a, b[i]) % MOD;
            a = pow(a, 10);
        }
        return ans;
    }
};
```

方法二：正序遍历

```
class Solution {
    const int MOD = 1337;
    int pow(int x, int n) {
        int res = 1;
        while (n) {
            if (n % 2) {
                res = (long) res * x % MOD;
            }
            x = (long) x * x % MOD;
            n /= 2;
        }
        return res;
    }
public:
    int superPow(int a, vector<int>& b) {
        int ans = 1;
        for (int e: b) {
            ans = (long) pow(ans, 10) * pow(a, e) % MOD;
        }
        return ans;
    }
};
```

