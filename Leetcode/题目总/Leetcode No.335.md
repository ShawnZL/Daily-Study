# Leetcode No.335

###### [335. 路径交叉](https://leetcode-cn.com/problems/self-crossing/)

[所有情况总结链接](https://leetcode-cn.com/problems/self-crossing/solution/lu-jing-jiao-cha-by-leetcode-solution-dekx/)

方法一：归纳法

```c++
class Solution {
public:
    bool isSelfCrossing(vector<int>& distance) {
        int n = distance.size();
        for (int i = 3; i < n; i++) {
            //第一类路径交叉情况
            if (distance[i] >= distance[i - 2] && distance[i - 1] <= distance[i - 3])
                return true;
            //第二类交叉情况
            if (i == 4 && (distance[3] == distance[1] && distance[4] >= distance[2] - distance[0])) 
                return true;
            //第三类路径交叉情况
            if (i >= 5 && (distance[i - 3] - distance[i - 5] <= distance[i - 1]
                && distance[i - 1] <= distance[i - 3]
                && distance[i] >= distance[i - 2] - distance[i - 4]
                && distance[i - 2] > distance[i - 4])) 
                return true;
        }
        return false;
    }
};
```

方法二：归纳不相交情况

```c++
class Solution {
public:
    bool isSelfCrossing(vector<int>& distance) {
        int n = distance.size();

        // 处理第 1 种情况
        int i = 0;
        while (i < n && (i < 2 || distance[i] > distance[i - 2])) {
            ++i;
        }

        if (i == n) {
            return false;
        }

        // 处理第 j 次移动的情况
        if ((i == 3 && distance[i] == distance[i - 2])
            || (i >= 4 && distance[i] >= distance[i - 2] - distance[i - 4])) {
            distance[i - 1] -= distance[i - 3];
        }
        ++i;

        // 处理第 2 种情况
        while (i < n && distance[i] < distance[i - 2]) {
            ++i;
        }

        return i != n;
    }
};
```

