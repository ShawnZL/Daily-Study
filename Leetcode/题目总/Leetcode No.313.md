# Leetcode No.313

###### [313. 超级丑数](https://leetcode-cn.com/problems/super-ugly-number/)

[264. 丑数 II](https://leetcode-cn.com/problems/ugly-number-ii/)

利用最小堆实现，每一次取出栈顶元素x，x是最小丑数，对于数组primes中元素p，px也是超级丑数，再次送入堆中，防止重复元素，使用哈希集合去重

```c++
//升序队列，小顶堆
priority_queue <int,vector<int>,greater<int> > q;
//降序队列，大顶堆
priority_queue <int,vector<int>,less<int> >q;

//greater和less是std实现的两个仿函数（就是使一个类的使用看上去像一个函数。其实现就是类中实现一个operator()，这个类就有了类似函数的行为，就是一个仿函数类了）
```

insert().second表示插入成功的标志。返回true和false

```c++
class Solution {
public:
    int nthSuperUglyNumber(int n, vector<int>& primes) {
        unordered_set<long> seen;
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
                    //printf("%d\n", next);
                }
            }
        }
        return ugly;
    }
};
```

方法二：动态规划：

dp[i] 就表示第i个丑数，创建与数组primes 相同长度的数组pointers，表示下一个超级丑数是当前指针指向的超级丑数乘以对应的质因数。初始时，数组 pointers 的元素值都是 1。

当 2 ≤ i ≤ n时，令 dp[i] = min{dp[pointers[j]]×primes[j]}，然后对于每个0 ≤ j < m，分别比较dp[i] 和dp[pointers[j]]×primes[j] 是否相等，如果相等则将pointers[j] 加 1

```c++
class Solution {
public:
    int nthSuperUglyNumber(int n, vector<int>& primes) {
        vector<int> dp(n + 1);
        dp[1] = 1;
        int m = primes.size();
        vector<int> pointers(m, 1);
        for (int i = 2; i <= n; i++) {
            //vector<int> nums(m);
            int minNum = INT_MAX;
            for (int j = 0; j < m; j++) {
                int temp = dp[pointers[j]] * primes[j];
                minNum = min(minNum, temp);
            }
            dp[i] = minNum;
            for (int j = 0; j < m; j++) {
                //这里这个数字已经乘过了,该换一下个乘数
                if (minNum == primes[j] * dp[pointers[j]]) {
                    pointers[j]++;
                }
            }
        }
        return dp[n];
    }
};
```

