# Leetcode No.1894

###### [1894. 找到需要补充粉笔的学生编号](https://leetcode-cn.com/problems/find-the-student-that-will-replace-the-chalk/)

方法一：模拟这个过程，首先计算出来总数，然后对于利用k对总数取余，

需要注意的问题就是取int有可能出现问题就是字符越界

```c++
class Solution {
public:
    int chalkReplacer(vector<int>& chalk, int k) {
        long long sum = 0, len = chalk.size();
        for (int i = 0; i < len; i++) 
            sum += chalk[i];
        long long last = k % sum;
        int flag = 0;
        while(last >= chalk[flag]) {
            last -= chalk[flag++];
        }
        return flag;
    }
};
```

方法二：针对于第二次遍历，我们可以使用二分法进行加速，在第一边遍历过程中，我们记录下前缀和

```c++
class Solution {
public:
    int chalkReplacer(vector<int>& chalk, int k) {
        int len = chalk.size();
        if (chalk[0] > k) return 0;

        for (int i = 1; i < len; i++) {
            chalk[i] += chalk[i - 1];
            if (chalk[i] > k) 
                return i;
        }

        k %= chalk.back();
        return upper_bound(chalk.begin(), chalk.end(), k) - chalk.begin();
    }
};
```

