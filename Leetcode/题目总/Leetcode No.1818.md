# Leetcode No.1818

```c++
class Solution {
public:
    int minAbsoluteSumDiff(vector<int>& nums1, vector<int>& nums2) {
        int res1 = 0, len1 = nums1.size();
        vector<vector<int>> temp (len1, vector<int>(len1));
        for (int i = 0; i < len1; i++) {
            for (int j = 0; j < len1; j++) {
                int tes = abs(nums1[i] - nums2[j]);
                temp[i][j] = tes;
                temp[j][i] = tes;
            }
            res1 += abs(nums1[i] - nums2[i]);
            //printf("%d\n", res1);
        }
        //模拟计算过程
        //printf("%d", res1);
        if(res1 == 0) return 0;//俩个数组相同
        int minres = res1;
        for (int i = 0; i < len1; i++) {//更换的数据
            for (int j = 0; j < len1; j++) {
                if(i != j) {
                    int res2 = res1 - temp[j][j] + temp[i][j];
                    if(res2 < minres) {
                        minres = res2;
                        //printf("%d %d\n", i, j);
                    } 
                }
            }
        }
        int mod = 1e9+7;
        return minres % mod;
    }
};
```



```c++
class Solution {
public:
    static constexpr int mod = 1'000'000'007;

    int minAbsoluteSumDiff(vector<int>& nums1, vector<int>& nums2) {
        vector<int> rec(nums1);
        sort(rec.begin(), rec.end());
        int sum = 0, maxn = 0;
        int n = nums1.size();
        for (int i = 0; i < n; i++) {
            int diff = abs(nums1[i] - nums2[i]);
            sum = (sum + diff) % mod;
            int j = lower_bound(rec.begin(), rec.end(), nums2[i]) - rec.begin();
            //lower_bound 返回的是数据的地址 
            if (j < n) {
                maxn = max(maxn, diff - (rec[j] - nums2[i]));
            }
            if (j > 0) {
                maxn = max(maxn, diff - (nums2[i] - rec[j - 1]));
            }
        }
        return (sum - maxn + mod) % mod;
    }
};

```



方法一：暴力法无法通过leetcode的测试。

方法二：
$$
|nums1[i] - nums2[i]| - |nums1[j] - nums2[i]|
$$
就是希望可以最大化该差值，使得答案最小。在循环过程中，首先确定是i的数值，当i确定，相当于前半部分的值固定，接下来就是考虑使后半部分的值最小。那就需要nums1[j]的值与nums2[i]的值接近，使用二分法lower_bound(还有upper_bound)寻找最接近的元素。**需要注意的是，该元素既可能大于等于nums2[i]，也可能小于nums2[i]，因此我们需要各检查一次。**