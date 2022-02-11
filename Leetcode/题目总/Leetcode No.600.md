# Leetcode No.600

###### [600. 不含连续1的非负整数](https://leetcode-cn.com/problems/non-negative-integers-without-consecutive-ones/)

方法一：动态规划

我们可以使用01字典树的形式表示，其中每一条从根结点到叶结点的路径都是一个小于等于n的非负整数。在由所有小于等于 n的非负整数构成的 01 字典树中，找出不包含连续 1 的从根结点到叶结点的路径数量。以 n = 6 = (110) 为例，我们可以发现

对于01字典树中的两个结点n1和n2，如果他们高度相同，节点的值也相同，并且以他们为根节点的两棵子树都是满二叉树，那么他们包含无连续1的从根结点到叶结点的路径个数相同

对于 01 字典树中的两个结 n1 和 n2 的子结点，并且它们的值都是 1，那么所有经过 n1和 n2 的从根结点到叶结点的路径都是包含连续的1

[答案解析](https://leetcode-cn.com/problems/non-negative-integers-without-consecutive-ones/solution/suan-fa-xiao-ai-wo-lai-gei-ni-jie-shi-qi-4nh4/)

```c++
class Solution {
public:
    int findIntegers(int n) {
        // 预处理第 i 层满二叉树的路径数量
        vector<int> dp(31);
        dp[0] = dp[1] = 1;
        for (int i = 2; i < 31; ++i) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }

        // pre 记录上一层的根节点值，res 记录最终路径数
        int pre = 0, res = 0;
        for (int i = 29; i >= 0; --i) {
            int val = 1 << i;
            // if 语句判断 当前子树是否有右子树
            // n&(1<<i)是将左移i位的1与n进行按位与，即为保留n的第i位，其余位置零
            if ((n & val) != 0) {
                // 有右子树
                n -= val;
                res += dp[i + 1]; // 先将左子树（满二叉树）的路径加到结果中

                // 处理右子树
                if (pre == 1) {
                    // 上一层为 1，之后要处理的右子树根节点肯定也为 1
                    // 此时连续两个 1，不满足题意，直接退出
                    break;
                }
                // 标记当前根节点为 1
                pre = 1;
            } else {
                // 无右子树，此时不能保证左子树是否为满二叉树，所以要在下一层再继续判断
                pre = 0;
            }

            if (i == 0) {
                ++res;
            }
        }

        return res;
    }
};

```

