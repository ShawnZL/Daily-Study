# Leetcode No.786

###### [786. 第 K 个最小的素数分数](https://leetcode-cn.com/problems/k-th-smallest-prime-fraction/)

方法一：

记数组 arr 的长度为 n。将所有数组放进进行自定排序，规划将所有数组元素按照升序排序。a/b和c/d比较
$$
a*d<b*c
$$

```c++
class Solution {
public:
    vector<int> kthSmallestPrimeFraction(vector<int>& arr, int k) {
        int len = arr.size();
        vector<pair<int, int>> temp;
        for (int i = 0; i < len; i++) {
            for (int j = i + 1; j < len; j++) {
                temp.emplace_back(arr[i], arr[j]);//注意这个地方工作方式！
            }
        }
        sort(temp.begin(), temp.end(), [&](const auto& x, const auto& y){
            return x.first * y.second < x.second * y.first;
        });
        return {temp[k - 1].first, temp[k - 1].second};
    }
};
```

emplace_back 支持隐式构造，push_back支持。

方法二：

当分母给定为 arr[j\] 时，分子可以在 0 - j-1 中选择。因为严格单调递增，所以函数也是递增。我们将每一个分母arr[j\] 看成一个长度为 j 的列表。、

这些分数是严格递增的，使用优先队列来得到答案。初始状态，我们先存储 n-1 个分数。在求解答案过程中，优先从其中选择最小的函数取出，同时放入 arr\[i+1] / arr\[j]。这样第 k 次取出的就是结果。

```c++
class Solution {
public:
    vector<int> kthSmallestPrimeFraction(vector<int>& arr, int k) {
        int len = arr.size();
        auto cmp = [&](const pair<int, int>& x, const pair<int, int>& y) {
            return arr[x.first] * arr[y.second] > arr[x.second] * arr[y.first];
        };

        priority_queue<pair<int, int>, vector<pair<int, int>>, decltype(cmp)> q(cmp); //定义为指向函数的指针
        //是为了解决复杂的类型声明而使用的关键字。
        for (int j = 1; j < len; j++) {
            q.emplace(0, j);
        }
        for (int _ = 1; _ < k; ++_) {
            auto [i, j] = q.top();
            q.pop();
            if (i + 1 < j) {
                q.emplace(i + 1, j);
            }
        }
        return {arr[q.top().first], arr[q.top().second]};
    }
};
```

方法三：

[解析](https://leetcode-cn.com/problems/k-th-smallest-prime-fraction/solution/di-k-ge-zui-xiao-de-su-shu-fen-shu-by-le-argw/)

我们随便猜测一个实数，如果恰好有k个素数分数小于a，那么这k个素数分数中最大的那个就是第k个小的素数分数。利用双指针求答案。

使用指针j指向分母，这个指针每次可以向右移动一个位置，表示不同的分母。

使用指针i指向分子，这个指针会不断向右移动。保证arr\[i]/arr\[j] < a 一直成立。当指针i无法移动时，我们就知道arr\[0]···arr\[i] 都可以作为分子。但是arr\[i + 1] 以及以后的元素都不可以。即分母为arr\[j] 并且小于a的素数分数有i + 1个。

在j向右移动过程中，我们把每一个j 对应的 i+1都加入答案，这样双指针的过程完成，我们就可以得到有多少个小于a的素数分数。

```c++
class Solution {
public:
    vector<int> kthSmallestPrimeFraction(vector<int>& arr, int k) {
        int n = arr.size();
        double left = 0.0, right = 1.0;
        while (true) {
            double mid = (left + right) / 2;
            int i = -1, count = 0;
            //记录最大分数
            int x = 0, y = 1;

            for (int j = 1; j < n; ++j) {
                while ((double)arr[i + 1] / arr[j] < mid) {
                    ++i;
                    if (arr[i] * y > arr[j] * x) {
                        x = arr[i];
                        y = arr[j];
                    }
                }
                count += i + 1;
            }
            if (count == k) {
                return {x, y};
            }
            if (count < k) {
                left = mid;
            }
            else {
                right = mid;
            }
        }
    }
};
```

