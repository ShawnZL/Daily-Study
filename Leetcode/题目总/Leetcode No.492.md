# Leetcode No.492

###### [492. 构造矩形](https://leetcode-cn.com/problems/construct-the-rectangle/)

方法一：直接计算 sqrt() 方法为构造平方根

```c++
class Solution {
public:
    vector<int> constructRectangle(int area) {
        vector<int> res;
        int L = sqrt(area);
        for (int i = L; i >= 1; i--) {
            if (area % i == 0) {
                res.push_back(area / i);
                res.push_back(i);
                break;
            }
        }
        return res;
    }
};
```

