# Leetcode No.552

我的想法，就是利用dp\[i][j] 表示i长度下A的个数，其中L的个数和不合法的情况我们单独计算出现了问题，我只讨论了L的可能位置，但是并没有谈论不是L的其他位置的情况！！！！

```c++
class Solution {
public:
    const int MOD = 1e9 + 7;
    int checkRecord(int n) {
        if (n == 1) return 3;
        vector<vector<long>> dp(n + 1, vector<long>(3));
        //表示长度为i数据中A个数dp[i][2] 表示A >= 2;
        int countA = (n - 1) * (n - 2) / 2;
        int count = countA % MOD;
        dp[1][0] = 2; dp[1][1] = 1; dp[1][2] = 0;
        for (int i = 2; i <= n; i++) {
            dp[i][0] = (dp[i - 1][0] * 2) % MOD;
            dp[i][1] = (dp[i - 1][0] + dp[i - 1][1] * 2) % MOD;
            //dp[i][2] = (dp[i - 1][1] + dp[i - 1][2] * 3) % MOD;
        }
        //int temp = (int)((double)pow(3, n)) % MOD;
        int ans = (int)(dp[n][0] + dp[n][1] - countA) % MOD;
        return ans;
    }
};
```

dp\[i]\[j][k] 表示前i天有j个A且结尾有连续K个L的可奖励出勤记录，0 <= j <= 1, 0 <= k <= 2。dp[0]\[0][0] = 1

当第i天记录为‘P’，前i天和前 i - 1天相比 A的数量不变，连续L的数量清零
$$
dp[i][j][0] = dp[i][j][0] + dp[i - 1][j][0] + dp[i-1][j][1] + dp[i-1][j][2]
$$
当出勤纪律为A，前i天和前 i - 1天相比 A的数量加1，连续L的数量清零
$$
dp[i][1][0]=dp[i][1][0] + dp[i-1][0][0]+dp[i-1][0][1]+dp[i-1][0][2]
$$
当出勤纪律为L，前i天和前 i - 1天相比 A的数量不变，连续L的数量+1
$$
dp[i][j][k]=dp[i][j][k]+dp[i-1][j][k-1]
$$
第二个为空间优化

```c++
class Solution {
public:
    static constexpr int MOD = 1e9 + 7;
    int checkRecord(int n) {
        vector<vector<vector<int>>> dp(n + 1, vector<vector<int>>(2, vector<int>(3)));
        //长度，A的数量，结尾连续L的数量
        dp[0][0][0] = 1;
        for (int i = 1; i <= n; i++) {
            // 以 P 结尾的数量 此时L连续值变为0
            for (int j = 0; j <= 1; j++) {
                for (int k = 0; k <= 2; k++) {
                    dp[i][j][0] = (dp[i][j][0] + dp[i - 1][j][k]) % MOD;
                }
            }
            // 以 A 结尾的数量，此时A要严格小于1, A = 0 所以我们选择加dp[][0][]
            for (int k = 0; k <= 2; k++) {
                dp[i][1][0] = (dp[i][1][0] + dp[i - 1][0][k]) % MOD;
            }
            //以 L 结尾的数量 L < 2 L可以取0，1，
            for (int j = 0; j <= 1; j++) {
                for (int k = 1; k <= 2; k++) {
                    dp[i][j][k] = (dp[i][j][k] + dp[i - 1][j][k - 1]) % MOD;
                }
            }
        }
        int sum = 0;
        for (int j = 0; j <= 1; j++) {
            for (int k = 0; k <= 2; k++) {
                sum = (sum + dp[n][j][k]) % MOD;
            }
        }
        return sum;
    }
};
```

```c++
class Solution {
public:
    static constexpr int MOD = 1'000'000'007;

    int checkRecord(int n) {
        int dp[2][3];  // A 的数量，结尾连续 L 的数量
        memset(dp, 0, sizeof(dp));
        dp[0][0] = 1;
        for (int i = 1; i <= n; i++) {
            int dpNew[2][3];  // A 的数量，结尾连续 L 的数量
            memset(dpNew, 0, sizeof(dpNew));
            // 以 P 结尾的数量
            for (int j = 0; j <= 1; j++) {
                for (int k = 0; k <= 2; k++) {
                    dpNew[j][0] = (dpNew[j][0] + dp[j][k]) % MOD;
                }
            }
            // 以 A 结尾的数量
            for (int k = 0; k <= 2; k++) {
                dpNew[1][0] = (dpNew[1][0] + dp[0][k]) % MOD;
            }
            // 以 L 结尾的数量
            for (int j = 0; j <= 1; j++) {
                for (int k = 1; k <= 2; k++) {
                    dpNew[j][k] = (dpNew[j][k] + dp[j][k - 1]) % MOD;
                }
            }
            memcpy(dp, dpNew, sizeof(dp));
        }
        int sum = 0;
        for (int j = 0; j <= 1; j++) {
            for (int k = 0; k <= 2; k++) {
                sum = (sum + dp[j][k]) % MOD;
            }
        }
        return sum;
    }
};
```

矩阵快速幂算法

将 j 和 k 整合进入一个维度中，dp[i]\[j * 3 + k] 

```c++
class Solution {
public:
    static constexpr int MOD = 1'000'000'007;

    vector<vector<long>> pow(vector<vector<long>> mat, int n) {
        vector<vector<long>> ret = {{1, 0, 0, 0, 0, 0}};
        while (n > 0) {
            if ((n & 1) == 1) {
                ret = multiply(ret, mat);
            }
            n >>= 1;
            mat = multiply(mat, mat);
        }
        return ret;
    }

    vector<vector<long>> multiply(vector<vector<long>> a, vector<vector<long>> b) {
        int rows = a.size(), columns = b[0].size(), temp = b.size();
        vector<vector<long>> c(rows, vector<long>(columns));
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {
                for (int k = 0; k < temp; k++) {
                    c[i][j] += a[i][k] * b[k][j];
                    c[i][j] %= MOD;
                }
            }
        }
        return c;
    }

    int checkRecord(int n) {
        vector<vector<long>> mat = {{1, 1, 0, 1, 0, 0}, {1, 0, 1, 1, 0, 0}, {1, 0, 0, 1, 0, 0}, {0, 0, 0, 1, 1, 0}, {0, 0, 0, 1, 0, 1}, {0, 0, 0, 1, 0, 0}};
        vector<vector<long>> res = pow(mat, n);
        long sum = accumulate(res[0].begin(), res[0].end(), 0ll);
        return (int)(sum % MOD);
    }
};

```

