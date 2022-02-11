# Leetcode No.326

###### [326. 3的幂](https://leetcode-cn.com/problems/power-of-three/)

方法一：直接暴力除，查看余数

```c++
class Solution {
public:
    bool isPowerOfThree(int n) {
        while(n >= 3) {
            int last = n % 3;
            if (last != 0) return false;
            n /= 3;
        }
        if (n == 1) return true;
        return false;
    }
};
```

方法二：利用 int 中最大3的幂数3^19^=116226146，查看是否为其约数。

```c++
class Solution {
public:
    bool isPowerOfThree(int n) {
        return n > 0 && 1162261467 % n == 0;
    }
};
```

