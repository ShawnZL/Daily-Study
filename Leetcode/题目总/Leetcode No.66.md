# Leetcode No.66

###### [66. 加一](https://leetcode-cn.com/problems/plus-one/)

方法一：利用 while 循环保证在数组范围内不越界

```c++
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        int len = digits.size();
        int flag = 1, count = len - 1, temp = 0;
        while(flag != 0 && count >= 0) {
            temp = digits[count] + flag;
            digits[count] = temp % 10;
            flag = temp / 10;
            count--;
            //printf("%d ", digits[count]);
        }
        if (flag) digits.insert(digits.begin(), flag);
        return digits;
    }
};
```

