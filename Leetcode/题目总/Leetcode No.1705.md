# Leetcode No.1705

###### [1705. 吃苹果的最大数目](https://leetcode-cn.com/problems/maximum-number-of-eaten-apples/)

我自己没有完成的版本，有些思路上边的问题。！

```c++
class Solution {
public:
    int eatenApples(vector<int>& apples, vector<int>& days) {
        int len = apples.size(), res = 0, flag = 0, maxn = 1;
        vector<int> eat(len);
        //思路就是统计当前的能到最远的地方
        for (int i = 0; i < len; ++i) {
            maxn = max(maxn, i + days[i]);
            if (len < maxn) eat.resize(maxn);//跟新数组的最大长度
            int start = i;
            //针对于[2, 1, 10] [2, 10, 1]的优化for 循环不适合这里
            /*
            for (int j = 0; j < apples[i]; ++j) {
                eat[j + i] = 1;
                //printf("%d ", i + j);
            }*/
            while ()
            //printf("\n");
        }
        //针对于[2, 1, 10] [2, 10, 1]的优化
        for (int i = 0; i < maxn; ++i) 
            if (eat[i]) 
                res += 1;
        return res;
    }
};
```

答案，使用map做。

```c++
class Solution {
public:
    int eatenApples(vector<int>& apples, vector<int>& days) {
        int len = apples.size(), ans = 0;
        map<int, int> eatday;
        for (int i = 0; i < len || !eatday.empty(); ++i) {
            eatday.erase(i);//丢弃今天的腐烂苹果
            if (i < len && apples[i] > 0) {
                eatday[i + days[i]] += apples[i];// 直接添加
            }
            if (!eatday.empty()) {
                auto it = eatday.begin();
                --it->second;
                ans += 1;
                if (it->second == 0)
                    eatday.erase(it);
            }
        }
        return ans;
    }
};
```

