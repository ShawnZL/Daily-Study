# Leetcode No.441

###### [441. 排列硬币](https://leetcode-cn.com/problems/arranging-coins/)

方法一：直接暴力

这里要注意返回值！

```c++
class Solution {
public:
    int arrangeCoins(int n) {
        int sums = n, i = 1;
        while(sums >= i) {
            sums -= i;
            i++;
        }
        return i - 1;
    }
};
```

方法二：二分查找

根据等差数列求和公式可知，前k个完整阶梯行所需的硬币数量为
$$
total = (k + 1)*k/2
$$

向上取整！

```c++
class Solution {
public:
    int arrangeCoins(int n) {
        int left = 1, right = n;
        while (left < right) {
            int mid = (right - left + 1) / 2 + left;
            if ((long long) mid * (mid + 1) <= (long long) 2 * n) {
                left = mid;
            }
            else 
                right = mid - 1;
        }
        return left;
    }
};
```

方法三：数学方法

直接求解 2 元 2 次方程

```c++
class Solution {
public:
    int arrangeCoins(int n) {
        return (int) ((sqrt((long long) 8 * n + 1) - 1) / 2);
    }
};
```

