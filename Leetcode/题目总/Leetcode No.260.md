# Leetcode No.260

###### [260. 只出现一次的数字 III](https://leetcode-cn.com/problems/single-number-iii/)

方法一：使用map统计当前数据出现次数。

```
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        int len = nums.size();
        if (len <= 2) return nums;
        map<int, int> count;
        vector<int> res;
        for (int i = 0; i < len; i++) {
            count[nums[i]]++;
            if (count[nums[i]] >= 2) {
                count.erase(nums[i]);
            }
        }
        for (auto &coun: count) {
            res.push_back(coun.first);
        }
        return res;
    }
};
```

```c++
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        int len = nums.size();
        unordered_map<int, int> freq;
        for (int num : nums) {
            ++freq[num];
        }
        vector<int> ans;
        for (const auto& [num, count] : freq) {
            if (count == 1)
                ans.push_back(num);
        }
        return ans;
    }
};
```

方法二：位运算

异或 (xor) 数学符号为“⊕”，计算机符号为“xor”。如果a、b两个值不相同，则异或结果为1。如果a、b两个值相同，异或结果为0。其运算法则为
$$
a⊕b = (¬a ∧ b) ∨ (a ∧¬b)
$$
因此，我们可以使用位运算 x & -x 取出 x 的二进制表示中最低位那个 1，设其为第 l 位，那么 x1和 x2中的某一个数的二进制表示的第 l 位为 0，另一个数的二进制表示的第 l 位为 1。在这种情况下，x1⊕x2示的第 l 位才能为 1。

其中 x & (-x) 得到仅仅是最小为 1 的那一个位 例如1 和 -1 0001和 1111 异或就是 **0001**

这样一来，我们可以把 nums 中的所有元素分为两类，其中一类包含所有二进制表示的第 l 位为 0 的数字，另一类包含所有二进制表示的第 l 位为 1 的数字。

- 对于任意一个在数组 *nums* 中出现两次的元素，该元素的两次出现会被包含在同一类中；
- 对于任意一个在数组 *nums* 中只出现了一次的元素，即 *x*1 和 *x*2，它们会被包含在不同类中。

```c++
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        int xornum = 0;
        for (int num : nums) {
            xornum ^= num;
        }
        //防止溢出
        int lsb = (xornum == INT_MIN ? xornum : xornum & (-xornum));
        int type1 = 0, type2 = 0;
        for (int num: nums) {
            if (num & lsb) {
                type1 ^= num;
            }
            else {
                type2 ^= num;
            }
        }
        return {type1, type2};
    }
};
```

