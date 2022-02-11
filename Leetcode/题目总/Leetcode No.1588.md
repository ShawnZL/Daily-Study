# Leetcode No.1588

###### [1588. 所有奇数长度子数组的和](https://leetcode-cn.com/problems/sum-of-all-odd-length-subarrays/)

方法一：暴力算法，直接根据每一个的奇数和计算的到结果

```c++
class Solution {
public:
    int sumOddLengthSubarrays(vector<int>& arr) {
        int n = arr.size(), ans = 0;
        for (int i = 1; i <= n; i += 2) {
            for (int j = 0; j <= (n - i); j++) {
                for (int k = 0; k < i && j + k < n; k++) {
                    ans += arr[j + k];
                }
            }
        }
        return ans;
    }
};
```

方法二：前缀和，暴力算法的时间复杂度O(n\^3)，但是我们将计算每一个子数组的和的时间复杂度从O(n)降低到O(1)，则能将总时间复杂度从O(n\^3)到O(n\^2)

```c++
class Solution {
public:
    int sumOddLengthSubarrays(vector<int>& arr) {
        int n = arr.size();
        vector<int> sums(n + 1);
        for (int i = 0; i < n; i++) {
            sums[i + 1] = sums[i] + arr[i];
        }
        int ans = 0;
        for (int i = 0; i < n; i++) {
            for (int length = 1; i + length <= n; length += 2) {
                int end = i + length - 1;
                ans += sums[end + 1] - sums[i];
            }
        }
        return ans;
    }
};
```

**方法三：数学方法，计算每一个元素在多少个长度为奇数的子数组中出现，即可得到所有和**

对于元素arr[i]，记其左边和右边元素个数分别为leftcount和rightcount，则leftCount = i, rightCount = n -i - 1，如果元素arr[i]在一个长度为奇数的子数组中，那么arr[i]左边和右边的元素一定同时为奇数或者同时为偶数。

当元素arr[i]的左右元素同时为奇数，在区间[0, leftCount]内奇数有leftOdd = (leftCount + 1) / 2，在[0, rightCount] 范围内奇数有rightOdd = （rightCount + 1) / 2，包含arr[i]的子数组的个数为left * right。

当元素arr[i]左右同为偶数时，在区间[0, leftCount]内偶数有leftEven = leftCount / 2 + 1，在[0, rightCount] 范围内偶数有rightEven = rightCount / 2 + 1，包含arr[i]的子数组的个数为left * right

```c++
class Solution {
public:
    int sumOddLengthSubarrays(vector<int>& arr) {
        int n = arr.size(), ans = 0;
        for (int i = 0; i < n; i++) {
            int leftCount = i, rightCount = n - i - 1;
            int leftOdd = (leftCount + 1) / 2;
            int rightOdd = (rightCount + 1) / 2;
            int leftEven = leftCount / 2 + 1;
            int rightEven = rightCount / 2 + 1;
            ans += arr[i] * (leftOdd * rightOdd + leftEven * rightEven);
        }
        return ans;
    }
};
```

