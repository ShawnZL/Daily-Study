# Leetcode No.825

###### [825. 适龄的朋友](https://leetcode-cn.com/problems/friends-of-appropriate-ages/)

用户 x 会向 用户 y 发送好友请求，y 要满足
$$
0.5*ages[x]+7<ages[y]<=ages[x]
$$
当 ages[x] <= 14时，不存在满足条件的 ages[y]，所以我们只考虑 ages[x] >= 15的情况。

当 ages[x] 增加时，上述区间的左右边界均单调递增，因此如果我们将数组 ages 进行升序排序，那么就可以在遍历 ages[x] 的同时，使用两个指针 left 和 right 维护满足要求的 ages[y] 的左右边界。当 x 向后移动一个位置时：



```c++
class Solution {
public:
    int numFriendRequests(vector<int>& ages) {
        int n = ages.size();
        sort(ages.begin(), ages.end());
        int left = 0, right = 0, ans = 0;
        for (int age: ages) {
            if (age < 15)
                continue;
            while (ages[left] <= 0.5 * age + 7) 
                ++left;
            while (right + 1 < n && ages[right + 1] <= age) 
                ++right;
            ans += right - left;
        }
        return ans;
    }
};
```

