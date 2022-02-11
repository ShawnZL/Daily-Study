# Leetcode No.863

[二叉树中所有距离为k的节点](https://leetcode-cn.com/problems/all-nodes-distance-k-in-binary-tree/submissions/)

我自己写的方法思路是求出所有点到根的距离，之后要么加，要么减得到最后结果，但是对于这个

```
0,2,1,null,null,3]
3
3
```

输出结果为[1,2]，原因就是没有考虑是否在同一侧，因为这样1和3相加就是target。所有不合要求题目

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    //广度优先遍历
    vector<int> distanceK(TreeNode* root, TreeNode* target, int k) {
        queue<TreeNode*> test;
        TreeNode* ends = root;
        int flag;//记录目标到顶点的高度
        map<int, int> dis;//记录到达顶点的距离
        vector<int> res;//保存结果
        //int height = 0;//记录树高
        dis[root->val] = 0;
        test.push(ends);
        while (!test.empty()) {
            TreeNode* temp = test.front();//记录当前节点
            test.pop();//弹出头
            if (temp == target) {
                flag = dis[temp->val];
                //printf("%d", flag);
            }
            if (temp->left) {
                test.push(temp->left);
                dis[temp->left->val] = dis[temp->val] + 1;
            } 
            if (temp->right) {
                test.push(temp->right);
                dis[temp->right->val] = dis[temp->val] + 1;

            }
            /*if (temp == ends) {//为最后节点,跟新树高
                height++;
                ends = test.back();
                printf("-1 ");
            }*/
        }
        printf("flag = %d ", flag);
        for (auto& [e, adj] : dis) {
            if (adj + flag == k && e != target->val) 
                res.push_back(e);
            if (abs(adj - flag) == k && e != target->val)
                res.push_back(e);
            printf("%d=%d ", e, adj);
        }
        return res;
    }
};
```



完成的代码

方法一：深度优先搜索 + 哈希表
若将target 当作树的根结点，我们就能从target 出发，使用深度优先搜索去寻找与target 距离为 k 的所有结点，即深度为 k 的所有结点。由于输入的二叉树没有记录父结点，为此，我们从根结点root 出发，使用深度优先搜索遍历整棵树，同时用一个哈希表记录每个结点的父结点。然后从target 出发，使用深度优先搜索遍历整棵树，除了搜索左右儿子外，还可以顺着父结点向上搜索。代码实现时，由于每个结点值都是唯一的，哈希表的键可以用结点值代替。此外，为避免在深度优先搜索时重复访问结点，递归时额外传入来源结点from，在递归前比较目标结点是否与来源结点相同，不同的情况下才进行递归。

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
    unordered_map<int, TreeNode*> parents;
    vector<int> ans;
    void findParents(TreeNode* node) {
        if (node->left != nullptr) {
            parents[node->left->val] = node;
            findParents(node->left);
        }
        if (node->right != nullptr) {
            parents[node->right->val] = node;
            findParents(node->right);
        }
    }

    void findAns(TreeNode* node, TreeNode* from, int depth, int k) {
        if (node == nullptr) 
            return;
        if (depth == k) {
            ans.push_back(node->val);
            return;
        }
        if (node->left != from) {
            findAns(node->left, node, depth + 1, k);
        }
        if (node->right != from) {
            findAns(node->right, node, depth + 1, k);
        }
        if (parents[node->val] != from) {
            findAns(parents[node->val], node, depth + 1, k);
        }
    }

public:
    vector<int> distanceK(TreeNode* root, TreeNode* target, int k) {
        // 从 root 出发 DFS，记录每个结点的父结点
        findParents(root);
        // 从 target 出发 DFS，寻找所有深度为 k 的结点
        findAns(target, nullptr, 0, k);
        return ans;
    }
};
```

