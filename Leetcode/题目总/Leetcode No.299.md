# Leetcode No.299

###### [299. 猜数字游戏](https://leetcode-cn.com/problems/bulls-and-cows/)

方法一：直接遍历法

直接遍历，如果俩个地方数字对应相同，直接 ++bulls。

对于cow，我们在遍历位置数据不同的时候，直接使用使用两个数组统计各自的位置的数值，由于多余的数字无法匹配，对于 0 到 9 的每位数字，应取其在 secret 和 guess 中的出现次数的最小值。将每位数字出现次数的最小值累加，即为奶牛的个数。

```c++
class Solution {
public:
    string getHint(string secret, string guess) {
        int len1 = secret.size();
        int bulls = 0, cows = 0;
        vector<int> cntS(10), cntG(10);
        for (int i = 0; i < len1; ++i) {
            if (secret[i] == guess[i]) {
                ++bulls;
            }
            else {
                ++cntS[secret[i] - '0'];
                ++cntG[guess[i] - '0'];
            }
        }
        for (int i = 0; i < 10; ++i) {
            cows += min(cntS[i], cntG[i]);
        }
        return to_string(bulls) + 'A' + to_string(cows) + 'B';
    }
};
```

