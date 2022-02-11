# Leetcode No.476

###### [476. 数字的补数](https://leetcode-cn.com/problems/number-complement/)

方法一：位运算

根据题目要求，我们需要将 num 二进制表示每一位都取反。但是 int 为 32 位存储结构，所有我们需要找到 num 表示最高位的那个 1，再将这个 1 以及更低的位进行取反。

如果 num 二进制表示最高位的 11 是第 i ( 0 ≤ i ≤ 30) 位，那么一定有：
$$
2^i≤num≤2^i*2)
$$
在这之后，我们就可以遍历 num 的第 0∼i 个二进制位，将它们依次进行取反。我们也可以用更高效的方式，构造掩码 mask=2 ^ ( i + 1) −1，它是一个 i+1 位的二进制数，并且每一位都是 11。我们将 num 与 mask 进行异或运算，即可得到答案。

```c++
class Solution {
public:
    int findComplement(int num) {
        int highbit = 0;
        for (int i = 0; i <= 30; i++) {
            if (num >= (1 << i)) {
                highbit = i;
                //printf("%d %d\n", highbit, 1 << i);
            }
            else { 
                //printf("%d %d\n", i, 1 << i);
                break;
            }
        }
        int mask = (highbit == 30 ? 0x7fffffff : (1 << (highbit + 1)) - 1);
        return num ^ mask;
    }
};
```

<< 二进制左移运算符。将一个运算对象的各二进制位全部左移若干位（左边的二进制位丢弃，右边补0）即 A << 2 A 向左移动 2 位