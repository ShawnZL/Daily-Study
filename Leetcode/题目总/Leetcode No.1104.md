# Leetcode No.1104

[二叉树寻路](https://leetcode-cn.com/problems/path-in-zigzag-labelled-binary-tree/submissions/)

本题思路利用满二叉树的性质，父节点序号与子节点序号关系为father = son / 2,

其次，对于给定节点标号label。根据以上性质得到该节点到根的路径，对于偶数行按照从右到左的白标记情况，可以先按照从左到右的顺序记录，再转换。

1.当 i 是奇数时，第 i 行的每个节点的「从左到右标号」即为该节点的实际标号；

2.当 i 是偶数时，如果第 i 行的一个节点的「从左到右标号」为val，则该节点的实际标号为 2^{i-1} + 2^i - 1 - val

其次注意如果给定节点位于偶数行，一定要开始就进行转换

```c++
class Solution {
public:
    int getreverse(int label, int row) {
        return pow(2, row) + pow(2, (row - 1)) - label - 1;
    }
    vector<int> pathInZigZagTree(int label) {
        vector<int> ans;
        int temp = 1, row = 0;
        while (temp <= label) {
            temp *= 2;
            row++;
        }
        ans.push_back(label);
        ///////////////////
        if (row % 2 == 0) {
            label = getreverse(label, row);
        }
        //////////////////////
        row--;//减去目标节点所在的一层
        //printf("%d %d ", row, label);
        while (label != 1) {
            if (row % 2 == 1) {
                ans.push_back(label / 2);
            }
            else {
                ans.push_back(getreverse(label / 2, row));
            }
            label /= 2;
            row--;
        }
        //ans.push_back(1);
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```



```c++
class Solution {
public:
    int getReverse(int label, int row) {
        return (1 << row - 1) + (1 << row) - 1 - label;
    }

    vector<int> pathInZigZagTree(int label) {
        int row = 1, rowStart = 1;
        while (rowStart * 2 <= label) {
            row++;
            rowStart *= 2;
        }
        if (row % 2 == 0) {
            label = getReverse(label, row);
        }
        vector<int> path;
        while (row > 0) {
            if (row % 2 == 0) {
                path.push_back(getReverse(label, row));
            } else {
                path.push_back(label);
            }
            row--;
            label >>= 1;
        }
        reverse(path.begin(), path.end());
        return path;
    }
};

```

