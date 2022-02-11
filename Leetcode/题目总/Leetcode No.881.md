# Leetcode No.881

###### [881. 救生艇](https://leetcode-cn.com/problems/boats-to-save-people/)

首先分析本体，我们首先确保了没有超出limit限制，那么每一个人都可乘船，就是寻找与limit相差最少的两数之和

方法一：使用双指针法，左指针left表示当前未选择最小数，右指针right表示当前未选择最大数，如果left + right > limt,那么left还有可能匹配，但right已经没有机会匹配，他只能自己坐船，如果left + right <= limt， 那么二者同时上船

```c++
class Solution {
public:
    int numRescueBoats(vector<int>& people, int limit) {
        int n = people.size(), ans = 0, left = 0, right = n - 1;
        sort(people.begin(), people.end());
        //left表示当前最小值的下标，right表示最大值下标
        while (left <= right) {   
          //表示当前left值加上right值大于limit，那么right无法组合，将right自动一船
            if (people[left] + people[right] > limit) {
                right--;
                ans++;
            }
            //people[left] + people[right] <= limit
            else {
                left++;
                right--;
                ans++;
            }
        }
        return ans;
    }
};
```

