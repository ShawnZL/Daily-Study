# Leetcode No.1005

###### [1005. K 次取反后最大化的数组和](https://leetcode-cn.com/problems/maximize-sum-of-array-after-k-negations/)

数组和最大，应该明确一点就是我们应当优先修改负数，而且是最小的负数。总结就是永远修改最小的数字（包括0）

方法一：

当给定的 K 小于等于数组中负数的个数时，我们按照上述方法从小到大依次修改每一个负数即可。但如果 K 的值较大，那么我们不得不去修改非负数（即正数或者 0）了。由于修改 0 对数组的和不会有影响，而修改正数会使得数组的和减小，因此：

​	·如果数组中存在 0，那么我们可以对它进行多次修改，直到把剩余的修改次数用完；

​	·如果数组中不存在 0 并且剩余的修改次数是偶数，由于对同一个数修改两次等价于不进行修改，因此我们也可以在不减小数组的和的前提下，把修改次数用完；

​	·如果数组中不存在 0 并且剩余的修改次数是奇数，那么我们必然需要使用单独的一次修改将一个正数变为负数（剩余的修改次数为偶数，就不会减小数组的和）。为了使得数组的和尽可能大，我们就选择那个最小的正数。需要注意的是，在之前将负数修改为正数的过程中，可能出现了（相较于原始数组中最小的正数）更小的正数，这一点不能忽略。

```c++
class Solution {
public:
    int largestSumAfterKNegations(vector<int>& nums, int k) {
        unordered_map<int, int> freq;
        for (int num: nums) {
            freq[num] += 1;
        }
        int ans = accumulate(nums.begin(), nums.end(), 0);
        //accumulate(vec起始相加位置,vec最后一个参数位置，累加的初始值)
        for (int i = -100; i < 0; i++) {
            if (freq[i]) {
                int ops = min(k, freq[i]);
                ans += (-i) * ops * 2;
                freq[i] -= ops;
                freq[-i] += ops;
                k -= ops;
                if (k == 0) {
                    break;
                }
            }
        }
        if (k > 0 && k % 2 == 1 && !freq[0]) {
            for (int i = 1; i <= 100; i++) {
                if (freq[i]) {
                    ans -= i * 2;
                    break;
                }
            }
        }
        return ans;
    }
};
```

方法二：使用小根堆进行运算

priority_queue<Type, Container, Functional> 

Functional 如果没有定义就需要自己定义

priority_queue<int, vector\<int>, greater\<int>\> q; //小根堆

priority_queue<int\> q; //大根堆 ，省略最后两个参数就是大根堆

```c++
class Solution {
public:
    int largestSumAfterKNegations(vector<int>& nums, int k) {
        priority_queue<int, vector<int>, greater<int>> q;
        int sum = 0;
        for (int num: nums) {
            q.push(num);
            sum += num;
        }
        while (k) {
            k--;
            int temp = q.top();
            sum += (-temp) * 2;
            q.pop();
            q.push(-temp);
        }
        return sum;
    }
};
```

