# Leetcode No.575

###### [575. 分糖果](https://leetcode-cn.com/problems/distribute-candies/)

不知道为什么，这个题目总是想的解决方法是动态规划，因为有了需求糖果的总数，但是不能妹妹可以选择其中的糖果中的一个啊。

**但是仔细想，很简单**

方法一：贪心算法，因为已知妹妹所拿的糖果总数是确定的count，利用map计算所有的总类型数，如果当前类型总数超过count，但妹妹最多只能搞 count 糖果数，所以直接返回count，

如果当前总类数目小于 count，那么妹妹尽可能从每一类取一个，保证类型最多，剩下的任意选择，就可以得到结果！

```c++
class Solution {
public:
    int distributeCandies(vector<int>& candyType) {
        int len = candyType.size(), res = 0;
        int count = len / 2, temp = 0;//总计分到的糖果总数
        map<int, int> candySum;
        for (int candy: candyType) {
            candySum[candy]++;
        }
        if (candySum.size() >= count) return count;//此时总类超出运算范围，但妹妹最多只能搞到 总数/2 的糖果数
        else return candySum.size();
        /*while (temp != count) {
            for (auto &[candy, num] : candySum) {
                if (num >= 0) {
                    temp
                }
            }
        }*/
        
    }
};
```

利用set，其中相同元素只存储一次

```c++
class Solution {
public:
    int distributeCandies(vector<int>& candyType) {
        set<int> hashset;
        for (int candy: candyType)
            hashset.insert(candy);
        return min(hashset.size(), candyType.size() / 2);
    }
};
```

