# Leetcode No.987

[二叉树的垂序遍历](https://leetcode-cn.com/problems/vertical-order-traversal-of-a-binary-tree/)

本题思路是利用map保存坐标与值的关系，接下来就是排序过程。

本题的写作过程包含

​	1.**要记录unordered_map的不能包含一个没有初始化pair()， map可以，c++中没有给pair做的Hash函数。**

​	2.**结构体构造函数**

3. 自己的代码没有考虑左右问题，当左孩子和右孩子的坐标计算过程时不同的。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    struct Point {
        int row;
        int col;
        Point() : row(0), col(0) {}//无参数的构造函数
        Point(int x, int y) : row(x), col(y) {}//有参数的构造函数
    };

    map<Point, int> points;
    vector<Point> res;//用来记录Point，后续排序
    //有问题
    void Find(TreeNode* root, TreeNode* from, Point fromxy) {
        Point temp;
        if (root != nullptr) {
            if (from == nullptr) {
                temp = Point{0, 0};
                points[temp] = root->val;
                res.push_back(temp);
            }
            else {
                int row = fromxy.row + 1;
                int col = fromxy.col - 1;
                temp = Point{row, col};
                points[temp] = root->val;
                res.push_back(temp);
            }
        }
        if (root == nullptr)
            return;
        if (root->left != nullptr) {
            Find(root->left, root, temp);
        }
        if (root->right != nullptr) {
            Find(root->right, root, temp);
        }
    }
    
    static bool cmp(Point a, Point b) {
        return a.col < b.col; 
    }

    vector<vector<int>> verticalTraversal(TreeNode* root) {
        Find(root, nullptr, Point(0, 0));

        sort(res.begin(), res.end(), cmp);//排序

        vector<vector<int>> ans;
        int flag = res[0].col;//记录第一个数的col
        int i = 0, len = res.size();
        while (i < len) {
            vector<int> anstemp;
            while (res[i].col == flag) {//等于flag
                anstemp.push_back(points[res[i]]);
                i++;
            }
            flag = res[i].col;//跟新flag
            sort(anstemp.begin(), anstemp.end());
            ans.push_back(anstemp);
        }
        return ans;
    }
};
```



完成代码

emplace_back() 和 push_back() 的区别，就在于底层实现的机制不同。push_back() 向容器尾部添加元素时，首先会创建这个元素，然后再将这个元素拷贝或者移动到容器中（如果是拷贝的话，事后会自行销毁先前创建的这个元素）；而 emplace_back() 在实现时，则是直接在容器尾部创建这个元素，省去了拷贝或移动元素的过程。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> verticalTraversal(TreeNode* root) {
        vector<tuple<int, int, int>> nodes;

        function<void(TreeNode*, int, int)> dfs = [&](TreeNode* node, int row, int col) {
            if (!node)
                return;
            nodes.emplace_back(col, row, node->val);
            dfs(node->left, row + 1, col - 1);
            dfs(node->right, row + 1, col + 1);
        };
        dfs(root, 0, 0);
        sort(nodes.begin(), nodes.end());
        vector<vector<int>> ans;
        int lastcol = INT_MIN;
        for (const auto& [col, row, value] : nodes) {
            if (col != lastcol) {
                lastcol = col;
                ans.emplace_back();
            }
            ans.back().push_back(value);
        }
        return ans;
    }
};
```

