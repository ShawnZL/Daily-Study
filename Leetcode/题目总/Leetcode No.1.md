# Leetcode No.1

**暴力法**

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> res(2);
        int len = nums.size();
        for(int i = 0; i < len; i++) {
            for(int j = i + 1; j < len; j++) {
                if((nums[j] + nums[i]) == target)
                    return {i, j};
            }
        }
        return {};
    }
};
```

**哈希**

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> hashtable;
        int n = nums.size();
        for (int i = 0; i < n; i++) {
            auto it = hashtable.find(target - nums[i]);
            if (it != hashtable.end()) {//数据存在
                return {it->second, i};
            }
            hashtable[nums[i]] = i;
        }
        return {};
    }
};
```



