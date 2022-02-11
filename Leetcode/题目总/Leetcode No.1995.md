# Leetcode No.1995

###### [1995. 统计特殊四元组](https://leetcode-cn.com/problems/count-special-quadruplets/)

方法一：

使用哈希表存储 nums[d]

如果我们已经枚举了前三个下标 a, b, c，那么就已经知道等式左侧 nums[a] + nums[b] + nums[c] 的值，即为 nums[d]的值，对于下标 d 而言，它的取值范围是 c < d < n，那么我们可以使用哈希表统计数组 nums[c+1] 到 nums[n−1] 中每个元素出现的次数。这样一来，我们就可以直接从哈希表中获得满足等式的 dd 的个数，而不需要在 [c+1,n−1] 的范围内进行枚举了。

```
class Solution {
public:
    int countQuadruplets(vector<int>& nums) {
        int len = nums.size(), ans = 0;
        unordered_map<int, int> cnt;
        for (int c = len - 2; c >= 2; --c) {
            ++cnt[nums[c + 1]];
            for (int a = 0; a < c; ++a) {
                for (int b = a + 1; b < c; ++b) {
                    if (int sum = nums[a] + nums[b] + nums[c]; cnt.count(sum)) {
                        ans += cnt[sum];
                    }
                }
            }
        }
        return ans;
    }
};
```

方法二：

我们将等式左侧的 nums[c] 移动到右侧，变为
$$
nums[a] + nums[b] = nums[d]-nums[c]
$$
如果我们已经枚举了前两个下标 a, ba,b，那么就已经知道了等式左侧 nums[a]+nums[b] 的值，即为 nums[d]−nums[c] 的值。对于下标 c,d 而言，它的取值范围是 b < c < d < n，那么我们可以使用哈希表统计满足上述要求的每一种 nums[d]−nums[c] 出现的次数。这样一来，我们就可以直接从哈希表中获得满足等式的 c, dc,d 的个数，而不需要在[b+1,n−1] 的范围内进行枚举了。

```
class Solution {
public:
    int countQuadruplets(vector<int>& nums) {
        int n = nums.size();
        int ans = 0;
        unordered_map<int, int> cnt;
        for (int b = n - 3; b >= 1; --b) {
            for (int d = b + 2; d < n; ++d) {
                ++cnt[nums[d] - nums[b + 1]];
            }
            for (int a = 0; a < b; ++a) {
                if (int sum = nums[a] + nums[b]; cnt.count(sum)) {
                    ans += cnt[sum];
                }
            }
        }
        return ans;
    }
};
```

