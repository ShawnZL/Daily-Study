# Leetcode No.457

###### [457. 环形数组是否存在循环](https://leetcode-cn.com/problems/circular-array-loop/)

就是模拟计算过程，我们写了一个reading数组保存我们每一次遍历的数字，记录它为-1，如果我们本次遍历成功，就不管了，但是没有成功的话，就表示经过这个节点没有路径是可以循环的，判定是否经过该点。

flag 表示我们本次循环过程是否是全正数或者负数

还有一点就是关注数组循环问题，start + nums[i] 可能正上溢或者下溢， start = (start + n * 100) % n;利用取余%解决问题

同时还有一点就是nums[i]的数据可能自循环，加入一个判定(abs(nums[i]) % n == 0) 

还有就是循环数据的个数我们要限制，因为 1<len<= n

```c++
class Solution {
public:
    //本题目如果模拟整个过程，计算
    bool circularArrayLoop(vector<int>& nums) {
        int n = nums.size();//nums的长度,
        vector<int> reading(n, 0);
        //记录这个数据是否读过，因为我们在遍历数组一遍的过程中，一个下标i的向前的步数是确定的，也就是它向前的终点也是确定的，如果一次遍历没有找到终点，那么这条路径上所有的点都无法成为一个关键路径
        for (int i = 0; i < n; i++) {
            if (abs(nums[i]) % n == 0) {
                reading[i] = -1;
            }
            if (reading[i] == 0) {
                //当这个没有遍历过才是可以 
                int len = 0, start = i, flag, end = i;
                //flag表示这次过程是否为正负
                if (nums[start] > 0) flag = 1;
                else flag = -1;
                reading[start] = -1;//表示不归路
                do {
                    if (nums[start] * flag < 0) //符号不同 
                        break;
                    len++;//遍历字符个数
                    start += nums[start];
                    //此处可能会出现越界情况
                    //新做法
                    start = ((start + n) % n + n) % n;
                    //start = (start + n * 100) % n;
                    
                    if (reading[start] == -1) break;
                }while(start != end && len <= n);
                if (len > 1 && start == end) return true;
            } 
        }
        return false;
    }
};
```

快慢指针法：

快指针两步，慢指针一步

特别地，当nums[i] 为 n 的整倍数时，i 的后继节点即为 i 本身，此时循环长度k=1，不符合题目要求，因此我们需要跳过这种情况

```c++
class Solution {
public:
    bool circularArrayLoop(vector<int>& nums) {
        int n = nums.size();
        auto next = [&](int cur) {
            return ((cur + nums[cur]) % n + n) % n;
        };

        for (int i = 0; i < n; i++) {
            if (!nums[i]) {
                continue;
            }
            int slow = i, fast = next(i);
            //判断非零且方向相同
            while (nums[slow] * nums[fast] > 0 && nums[slow] * nums[next(fast)] > 0) {
                if (slow == fast) {
                    if (slow != next(slow)) {
                        return true;
                        //此时长度为1
                    } else {
                        break;
                    }
                }
                slow = next(slow);
                fast = next(next(fast));
            }
            //本次没有成功，将本路径上的点设置为0
            int add = i;
            while (nums[add] * nums[next(add)] > 0) {
                int tmp = add;
                add = next(add);
                nums[tmp] = 0;
            }
        }
        return false;
    }
};
```

