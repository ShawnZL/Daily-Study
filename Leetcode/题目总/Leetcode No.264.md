# Leetcode No.264

###### [264. 丑数 II](https://leetcode-cn.com/problems/ugly-number-ii/)

[313. 超级丑数](https://leetcode-cn.com/problems/super-ugly-number/)

解释见313

方法一，小根堆

```c++
class Solution {
public:
    int nthUglyNumber(int n) {
        unordered_set<long> seen;
        vector<int> primes = {2,3,5};
        priority_queue<long, vector<long>, greater<long>> heap;
    seen.insert(1);
    heap.push(1);
    int ugly = 0;
    for (int i = 0; i < n; i++) {
        long curr = heap.top();
        heap.pop();
        ugly = (int)curr;
        for (int prime : primes) {
            long next = curr * prime;
            if (seen.insert(next).second) {
                heap.push(next);
            }
        }
    }
    return ugly;
    }
};
```

方法二：动态规划

```c++
class Solution {
public:
    int nthUglyNumber(int n) {
        vector<int> primes = {2,3,5};
        vector<int> dp(n + 1);
        dp[1] = 1;
        vector<int> pointers(3, 1);
        for (int i = 2; i <= n; i++) {
            int minNum = INT_MAX;
            for (int j = 0; j < 3; j++) {
                int temp = dp[pointers[j]] * primes[j];
                minNum = min(minNum, temp);
            }
            dp[i] = minNum;
            for (int j = 0; j < 3; j++) {
                if (minNum == primes[j] * dp[pointers[j]]) 
                    pointers[j]++;
            }
        }
        return dp[n];
    }
};
```

方法三：

注意一点就是对于 2* 3这样的数字，我们在判定选择语句为三个if 而不是else-if，就是因为有的数据可以同时得到，但是我们仅仅需要保存一个结果就好了！

```
class Solution {
public:
    int nthUglyNumber(int n) {
        vector<int> dp(n);
        dp[0] = 1;
        int p2 = 0, p3 = 0, p5 = 0;
        for (int i = 1; i < n; i++) {
            dp[i] = min(dp[p2] * 2, min(dp[p3] * 3, dp[p5] * 5));
            if (dp[i] == 2 * dp[p2]) {
                p2++;
            }
            if (dp[i] == 3 * dp[p3]) {
                p3++;
            }
            if (dp[i] == 5 * dp[p5]){
                p5++;
            }
            //printf("%d ", dp[i]);
        }
        return dp[n - 1];
    }
};

```

