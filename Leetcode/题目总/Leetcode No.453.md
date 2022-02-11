# Leetcode No.453

###### [453. 最小操作次数使数组元素相等](https://leetcode-cn.com/problems/minimum-moves-to-equal-array-elements/)

方法一：数学

因为只需要找出让数组所有元素相等的最小操作次数，所以我们不需要考虑数组中各个元素的绝对值大小，即不需要真正算出数组中所有元素相等时的元素值，只需要考虑数组中元素的相对大小变化。

因此每次操作既可以理解为使 n - 1 个元素增加 1， 也可以理解使 1 个元素减少 1，显然，后者更有利于我们计算。

于是，要计算让数组中所有元素相等的操作数，我们只需要计算将数组中所有元素都减少到数组中元素最小值所需的操作数。

```c++
class Solution {
public:
    int minMoves(vector<int>& nums) {
        int len = nums.size();
        int minnum = *min_element(nums.begin(), nums.end());
        int res = 0;
        for (auto num: nums) {
            res += num - minnum;
        }
        return res;
    }
};
```

```python
class Solution:
    def minMoves(self, nums: List[int]) -> int:
        min_num = min(nums);
        res = 0
        for num in nums:
            res += num - min_num
        return res
```

