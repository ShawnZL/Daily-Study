# Leetcode No.443

###### [443. 压缩字符串](https://leetcode-cn.com/problems/string-compression/)

本题目有一个关键点就是在于我们不光要记录数据的个数，同时也要保存当前数据所存放的位置，所以我使用flag表示个数的数据位置，因为字母或符号肯定只有一个，将flag隔过去这个位置，计算完毕后，flag - 1就是字母所在位置，同时flag就是个数开始的位置，

**还有特别重要的一点就是我们对于数组，使用while在循环里执行i++，一定要先判定i是否越界，就如同代码while *(i < n* && 判定条件)**

```c++
class Solution {
public:
    int compress(vector<char>& chars) {
        int ans = 0, i = 0;
        int n = chars.size();
        if (n == 1) return 1;
        /*if (n == 2) {
            if (chars[0] == chars[1]) {
                chars[1] = 2 + '0';
            }
            return 2;
        }*/
        int flag = 1;//记录个数的位置
        while (i < n) {
            char temp = chars[i++];
            chars[flag - 1] = temp;//记录当前的数据
            int res = 1;//记录数字的个数
            while(i < n && chars[i] == temp) {//先判断是否越界
                i++;
                res++;
                //printf("%d %d", i);
            }
            if (res < 10) {
                if (res != 1) {
                    chars[flag++] = '0' + res;
                    ans += 1;
                }
                /*else {
                    ans += 1;
                }*///省略了这个代码，
            }
            else {
                vector<int> nums;
                while (res != 0) {
                    nums.push_back(res % 10);
                    res /= 10;
                }
                for (int j = nums.size() - 1; j >= 0; j--) {
                    chars[flag++] = '0' + nums[j];
                    ans++;
                }
            }
            ans++;//因为当前字母所在，必定加1
            //区别就是加上数字的位数
            flag++;//跨过字母所在位数
        }
        return ans;
    }
};
```

简单版

```c++
class Solution {
public:
    int compress(vector<char>& chars) {
        int ans = 0, n = chars.size();
        int flag = 1, i = 0;
        if (n == 1) return 1;
        while (i < n) {
            int nums = 1;
            char temp = chars[i++];
            chars[flag - 1] = temp;
            while(i < n && chars[i] == temp) {
                i++;
                nums++;
            }
            //统计字母个数
            if (nums < 10 && nums > 1) {
                chars[flag++] = '0' + nums;
            }
            else if (nums >= 10) {
                vector<int> count;
                while(nums != 0) {
                    count.push_back(nums % 10);
                    nums /= 10;
                }
                for (int j = count.size() - 1; j >= 0; j--) {
                    chars[flag++] = '0' + count[j];
                }
            }
            flag++;//过去下一个字母
        }
        return flag - 1;
    }
};
```

