# Leetcode No.367

###### [367. 有效的完全平方数](https://leetcode-cn.com/problems/valid-perfect-square/)

方法一：利用set直接记录所有平方数，判断数据是否存在

```c++
set<int> alp;
int init = [](){
    for (int i = 1; i < 46341; ++i) {
        alp.insert(i * i);
    }
    return 0;
}();

class Solution {
public:
    bool isPerfectSquare(int num) {
        return alp.count(num);
    }
};
```

```python
class Solution:
    def isPerfectSquare(self, num: int) -> bool:
        a = []
        for i in range(1, 46341):
            a.append(int(i * i))
        return num in a
```

方法二：二分查找

考虑使用二分查找来优化方法二中的搜索过程。因为 num 是正整数，所以若正整数 x 满足 x * x=num，则 xx 一定满足 1 ≤ x ≤ num。于是我们可以将 1 和 num 作为二分查找搜索区间的初始边界。

因为我们在移动左侧边界 left 和右侧边界 right 时，新的搜索区间都不会包含被检查的下标 mid，所以搜索区间的边界始终是我们没有检查过的。因此，当 left = right 时，我们仍需要检查 mid = (left + right) / 2。

```c++
class Solution {
public:
    bool isPerfectSquare(int num) {
        int left = 0, right = num;
        while (left <= right) {
            int mid = (right - left) / 2 + left;
            long square = (long) mid * mid;
            if (square < num) {
                left = mid + 1;
            }
            else if (square > num) {
                right = mid - 1;
            }
            else {
                return true;
            }
        }
        return false;
    }
};
```

方法三：泰勒级数

```c++
class Solution {
public:
    bool isPerfectSquare(int num) {
        double x0 = num;
        while (true) {
            double x1 = (x0 + num / x0) / 2;
            if (x0 - x1 < 1e-6) {
                break;
            }
            x0 = x1;
        }
        int x = (int) x0;
        return x * x == num;
    }
};
```

