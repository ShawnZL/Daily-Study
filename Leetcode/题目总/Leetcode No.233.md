# Leetcode No.233

###### [233. 数字 1 的个数](https://leetcode-cn.com/problems/number-of-digit-one/)

方法一：假设有n=abcde len=5 第三位--1-- 就是挑选1<= --1--<=abcde

当 c 前面的部分 <ab，即范围为 [0, ab)，此时必然满足「大小要求」，因此后面的部分可以任意取，即范围为 \[0, 99]。根据「乘法原理」，可得知此时数量为 ab * 100；

当 c 前面的部分 = ab, 这时候的大小关系却决于c:

c = 0 数量为0

c = 1 这时候的大小关系却决于后部分，后面的取值为\[0, de], 数量为 1 * (de + 1); 100就是加一的情况

c > 1 此时后边随意取数量为 1* 100

当c前面的部分 > ab 不满足，取 0

```c++
class Solution {
public:
    int countDigitOne(int n) {
        string s = to_string(n);
        int len = s.size();
        if (len == 1) return n > 0 ? 1 : 0;
        //计算第i位前缀代表的数值，和后缀代表的数值
        // 例如 abcde 则有 ps[2] = ab; ss[2] = de
        vector<int> ps(len, 0);
        vector<int> ss(len, 0);
        ss[0] = stoi(s.substr(1));
        for (int i = 1; i < len - 1; i++) {
            ps[i] = stoi(s.substr(0, i));
            ss[i] = stoi(s.substr(i + 1));
        }
        ps[len - 1] = stoi(s.substr(0, len - 1));
        int ans = 0;
        for (int i = 0; i < len; i++) {
            //x为当前位置，mlen为当前位置后面长度
            int x = s[i] - '0', mlen = len - i - 1;
            int prefix = ps[i], suffix = ss[i];
            int tot = 0;
            tot += prefix * pow(10, mlen);
            if (x == 0) {
            }
            else if (x == 1) {
                tot += suffix + 1;
            }
            else {
                tot += pow(10, mlen);
            }
            ans += tot;
        }
        return ans;
    }
};
```



官方：

```c++
class Solution {
public:
    int countDigitOne(int n) {
        // mulk 表示 10^k
        // 在下面的代码中，可以发现 k 并没有被直接使用到（都是使用 10^k）
        // 但为了让代码看起来更加直观，这里保留了 k
        long long mulk = 1;
        int ans = 0;
        for (int k = 0; n >= mulk; ++k) {
            ans += (n / (mulk * 10)) * mulk + min(max(n % (mulk * 10) - mulk + 1, 0LL), mulk);
            mulk *= 10;
        }
        return ans;
    }
};
```

