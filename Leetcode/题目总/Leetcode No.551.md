# Leetcode No.551

###### [551. 学生出勤记录 I](https://leetcode-cn.com/problems/student-attendance-record-i/)

本题目我设置三个变量统计A，P，L的个数，分别判定每个数据可以了，但是有一点就是迟到是要连续的三天，所以我在处理过程中，如果当前数据不是L，那么其他的count++，但是countL要设置为0，表示不连续，在实际运行过程，countP数据是不需要的，所以改进版本将会舍去，节省空间。

```c++
class Solution {
public:
    bool checkRecord(string s) {
        int n = s.length();
        int countA = 0, countL = 0;// countP = 0;
        for (int i = 0; i < n; i++) {
            if (s[i] == 'A') {
                countA++;
                countL = 0;
            }
            else if (s[i] == 'L') {
                countL++;
            }
            else {
                //countP++;
                countL = 0;
            }
            if (countA >= 2 || countL >= 3) {
                return false;
            } 
        }
        return true;
    }
};
```

