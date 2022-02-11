# Leetcode No.1337

[矩阵中战斗力最弱的k行](https://leetcode-cn.com/problems/the-k-weakest-rows-in-a-matrix/)

使用结构体做法

```c++
class Solution {
    struct nodes{
        int row;
        int num;
        nodes(int x, int y) : row(x), num(y){}
    };
public:
    static bool cmp(nodes x, nodes y) {
        if (x.num == y.num)
            return x.row < y.row;
        return x.num < y.num;
    }
    vector<int> kWeakestRows(vector<vector<int>>& mat, int k) {
        vector<nodes> tes;
        int m = mat.size(), n = mat[0].size();
        for (int i = 0; i < m; i++) {
            int temp = 0;
            for (int j = 0; j < n; j++) {
                if(mat[i][j] == 1) temp++;
                else break;
            }
            tes.push_back(nodes(i, temp));
        }
        ///*****二分法 注意pos其实值为-1；
        /*for (int i = 0; i < m; i++) {
            int l = 0, r = n - 1, pos = -1;
            while(l <= r) {
                int mid = (l + r) / 2;
                if (mat[i][mid] == 0) 
                    r = mid - 1;
                else {
                    pos = mid;//可能此时mid就是最有一个1
                    l = mid + 1;
                }
            }
            //printf("%d %d\n", i, pos);
            tes.push_back(nodes(i, pos));
        }*/
        
        sort(tes.begin(), tes.end(), cmp);
        vector<int> ans;
        for (int i = 0; i < k; i++) {
            ans.push_back(tes[i].row);
        }
        return ans;
    }
};
```



官方做法为使用二分查找寻找1的位置+堆的使用

priority_queue本质是一个堆。模板申明带3个参数：priority_queue<Type, Container, Functional>，其中Type 为数据类型，Container为保存数据的容器，Functional 为元素比较方式。

greater<type> 来自functional，表示按照从小到大，less表示从大到小。greater和less都重载了操作符()。

通过std::move，函数可以以非常简单的方式将左值引用转换为右值引用，可以避免不必要的拷贝操作。std::move是为性能而生。std::move是将对象的状态或者所有权从一个对象转移到另一个对象，只是转移，没有内存的搬迁或者内存拷贝。



左值是可以放在赋值号左边可以被赋值的值；左值必须要在内存中有实体；
右值当在赋值号右边取出值赋给其他变量的值；右值可以在内存也可以在CPU寄存器。

```c++
class Solution {
public:
    vector<int> kWeakestRows(vector<vector<int>>& mat, int k) {
        int m = mat.size(), n = mat[0].size();
        vector<pair<int, int>> power;
        for (int i = 0; i < m; i++) {
            int l =0, r = n - 1, pos = -1;
            while(l <= r) {
                int mid = (l + r) / 2;
                if (mat[i][mid] == 0) 
                    r = mid - 1;
                else {
                    pos = mid;
                    l = mid + 1;
                }
            }
            power.emplace_back(pos + 1, i);
        }

        priority_queue q(greater<pair<int, int>>(), move(power));
        vector<int> ans;
        for (int i = 0; i < k; i++) {
            ans.push_back(q.top().second);
            q.pop();
        }
        return ans;
    }
};
```

