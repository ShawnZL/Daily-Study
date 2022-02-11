# Leetcode No.446

###### [446. 等差数列划分 II - 子序列](https://leetcode-cn.com/problems/arithmetic-slices-ii-subsequence/)

由于尾项和公差可以确定一个等差数列，dp\[i][d]表示尾项为nums[i]，公差为d的弱等差子序列的个数

使用一个二重循环去遍历nums中所有的元素对(nums[i], nums[j]) 将nums[j] 和nums[i] 当作倒数第二项和尾项，公差就是 d = nums[i] - nums[j]。 由于公差相同，可以加到以nums[j] 结尾的公差为d的若等差子序列的尾部，同时(nums[j], nums[i]) 这一对元素也可以当作一个若等差子序列。我们现在拥有了合适的首项和第二项，当有合适尾项就可以构成(nums[j(以前)], nums[i(以前)], nums[i(现在)]) 一个三个的等差序列，而我们所做的就是先假设这个存在，并把这个加入我们的dp\[i][d]中，等到何时直接返回 it->second(已经计算了这个情况) 或者我们永远不使用这个情况。
$$
dp[i][d] += dp[j][d] + 1;
$$
由于题目要统计的等差子序列至少有三个元素，我们回顾上述二重循环，其中nums[i] 加到以 nums[j] 为尾项，公差为 d 的弱等差子序列的末尾」这一操作，实际上就构成了一个至少有三个元素的等差子序列，因此我们将循环中的dp\[j][d] 累加，即为答案。

代码实现时，由于 nums[i] 的范围很大，所以计算出的公差的范围也很大，我们可以将状态转移数组 f 的第二维用哈希表代替。



```c++
class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& nums) {
        int n = nums.size();
        if (n < 3) return 0;
        int ans = 0;
        vector<unordered_map<long long, int>> dp(n);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                //容易出错点就是 j< i
                long long d = 1LL * nums[i] - nums[j];
                //LL = long long 
                //1LL * nums 目的就是把nums转换为long long类型
                auto it = dp[j].find(d);
                int cnt = it == dp[j].end() ? 0 : it->second;
                ans += cnt;//此时我们加入的是上一次多加的一个可以成的序列
                //此时有了合适的尾项(nums[j(before)], nums[i(before)], nums[i])
                dp[i][d] += cnt + 1;//这个是为下一个准备的
                //执行dp = dp + cnt + 1;
            }
        }
        return ans;
    }
};
```

