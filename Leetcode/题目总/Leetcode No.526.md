# Leetcode No.526

###### [526. 优美的排列](https://leetcode-cn.com/problems/beautiful-arrangement/)

方法一回溯法

```c++
class Solution {
public:
    vector<vector<int>> match;
    vector<int> vis;
    int num;

    void backtrack(int index, int n) {
        if (index == n + 1) {
            num++;
            return;
        }
        for (auto &x : match[index]) {
            if (!vis[x]) {
                vis[x] = true;
                backtrack(index + 1, n);
                vis[x] = false;
            }
        }
    }
    int countArrangement(int n) {
        vis.resize(n + 1);
        match.resize(n + 1);
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                if (i % j == 0 || j % i == 0) {
                    match[i].push_back(j);
                }
            }
        }
        backtrack(1, n);
        return num;
    }
};
```



方法二：

__builtin_popcount() 和 Integer.bitCount同为计算一个数据中二进制表示中1的个数

^也可以表示特殊的二元运算符——逐位逻辑运算符（用于对数据的位进行操作），它表示的含义是逐位非或，要求两个整型操作数。从最小（即最右）的位开始，对操作数逐位操作；如果只有其中一个位为1，那么结果为1；否则为0

```c++
class Solution {
public:
    int countArrangement(int n) {
        vector<int> f(1 << n);
        f[0] = 1;
        for (int mask = 1; mask < (1 << n); mask++) {
            int num = __builtin_popcount(mask);
            for (int i = 0; i < n; i++) {
                if (mask & (1 << i) && (num % (i + 1) == 0 || (i + 1) % num == 0)) {
                    f[mask] += f[mask ^ (1 << i)];
                }
            }
        }
        return f[(1 << n) - 1];
    }
};

/*
// 用 mask 的二进制表示选取状态，n 个数字用 n 位表示，第 i 位为 1 代表数字 i+1 已被选取（i从0开始），n 中 1 的个数 m 代表前 m 位已放置
   // 例如：二进制 100110 共三个1，代表排列的前三位已放置数字，三个1分别在二进制第 1、2、5位置上(从右侧开始，从0开始计数）, 所以 2、3、6三个数字被选取，综合起来就是表示：2 3 6 这三个数字被放到了排列的前三位，三个数字完美排列方式未知，通过枚举 mask 进行计算

     class Solution {
         public int countArrangement(int n) {
             // 用来存储中间结果，f[6] = f[000110] = 数字2、3在前两位时的完美排列数量
             int[] f = new int[1 << n];
             f[0] = 1;
             // 通过 mask 进行枚举，最终目的是为了得到二进制 mask = (11..11)n 时，总的完美排列数 
             for (int mask = 1; mask < (1 << n); mask++) {
                 int num = Integer.bitCount(mask);
                 // 遍历 mask 的每一位，仍以 mask = 100110 为例，此 mask 代表 2 3 6三个数字在排列的前三位
                 // 求三个数字 2 3 6 的完美排列方式，则先确定2 3 6哪些数字能放到第三位，然后累加另外两个数字的完美排列数量来获得
                 // 2 3 6，第三位可以为 6，则 f[100110] += f[000110] (2、3在前两位时的完美排列数量)
                 // 2 3 6，第三位可以为 3，则 f[100110] += f[100010] (2、6在前两位时的完美排列数量)
                 for (int i = 0; i < n; i++) {
                     // mask & (1<<i) 用来判断 mask 第 i 位是否为 1，如果为 1，说明第 i+1 个数字被选取
                     // ((num % (i + 1)) == 0 || (i + 1) % num == 0) 判断被选取的数字 i+1 能否放到位置 num 上，
                     // 即：先从被选取的数字中找到能放到最高位 num 的数字，然后将剩余 num-1 个数字的完美排列方式累加到f[mask]中
                     if ((mask & (1 << i)) != 0 && ((num % (i + 1)) == 0 || (i + 1) % num == 0)) {
                         // mask ^ (1 << i) 将 mask 第 i 位设置为 0
                         f[mask] += f[mask ^ (1 << i)];
                     }
                 }
             }
             return f[(1 << n) - 1];
         }
     }


```

