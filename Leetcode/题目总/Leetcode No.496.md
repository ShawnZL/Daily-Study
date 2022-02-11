# Leetcode No.496



方法一：模拟运算过程

```c++
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        int len1 = nums1.size(), len2 = nums2.size();
        vector<int> res;
        for (int i = 0; i < len1; i++) {
            int flag, j = 0;
            while (j < len2 && nums2[j] != nums1[i]) {
                j++;
            }
            if (j == len2) res.push_back(-1);
            else {
                j++;
                while (j < len2 && nums2[j] < nums1[i]) {
                    j++;
                }
                j == len2 ? res.push_back(-1):res.push_back(nums2[j]);
            }
        }
        return res;
    }
};
```

方法二：单调栈 + 哈希表

我们可以预先处理 nums2，使查询nums1中的每个元素在 nums2 中对应位置的右边的第一个更大元素值时不需要遍历nums2，将问题分解为两个子问题；

第一个问题：如何更高效地计算nums2中每个元素右边的第一个更大值

第二个问题：如何存储第 1 个子问题的结果

利用单调栈来解决第 1 个子问题。倒序遍历nums2，并且单调栈中维护当前位置右边的更大的元素列表。从栈底到栈顶的元素是单调递减的。具体地，每次我们移动到数组中一个新的位置 i，就将当前单调栈中所有小于 nums 2[i] 的元素弹出单调栈，当前位置右边的第一个更大的元素即为栈顶元素，如果栈为空则说明当前位置右边没有更大的元素。随后我们将位置 i 的元素入栈。

**先找到最大的元素，之后才执行压栈操作！！！**

```c++
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        unordered_map<int, int> hashmap;
        stack<int> st;
        int len2 = nums2.size(), len1 = nums1.size();
        for (int i = len2 - 1; i >= 0; i--) {
            int num = nums2[i];
            while (!st.empty() && st.top() <= num) {
                st.pop();
            }
            hashmap[num] = st.empty() ? -1 : st.top();
            st.push(num);
        }
        vector<int> res(len1);
        for (int i = 0; i < len1; i++) {
            res[i] = hashmap[nums1[i]];
        }
        return res;
    }
};
```

