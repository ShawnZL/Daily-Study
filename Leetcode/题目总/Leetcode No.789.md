# Leetcode No.789

###### [789. 逃脱阻碍者](https://leetcode-cn.com/problems/escape-the-ghosts/)

首先补充知识，曼哈顿距离：两点在南北方向上的距离加上在东西方向上的距离
$$
 d(i, j) = |Xi - Xj| + |Yi - Yj|
$$
欧几里得距离：两点之间直线距离；曼哈顿距离可以大大地缩减运算时间，曼哈顿距离不是距离不变量，当坐标轴变动时，点间的距离就会不同。曼哈顿距离示意图在早期的计算机图形学中，屏幕是由像素构成，是整数，点的坐标也一般是整数，原因是浮点运算很昂贵，很慢而且有误差，如果直接使用AB的欧氏距离（欧几里德距离：在二维和三维空间中的欧氏距离的就是两点之间的距离），则必须要进行浮点运算，如果使用AC和CB，则只要计算加减法即可，这就大大提高了运算速度，而且不管累计运算多少次，都不会有误差。

曼哈顿距离性质：非负性，同一性，对称性，三角不等性：d(i,j)≤d(i,k)+d(k,j)从对象i到对象j的直接距离不会大于途经的任何其他对象k的距离和

利用Manhattan距离代表之间距离，如果有任何一个ghost比目标距离终点进，那么就可以在达到终点前拦截到。

```c++
class Solution {
public:
    int manhattanDistance(vector<int>& point1, vector<int>& point2) {
        return abs(point1[0] - point2[0]) + abs(point1[1] - point2[1]);
    }

    bool escapeGhosts(vector<vector<int>>& ghosts, vector<int>& target) {
        vector<int> source(2);
        int distance = manhattanDistance(source, target);
        for (auto& ghost : ghosts) {
            int ghostDistance = manhattanDistance(ghost, target);
            if (ghostDistance <= distance) {
                return false;
            }
        }
        return true;
    }
};
```

