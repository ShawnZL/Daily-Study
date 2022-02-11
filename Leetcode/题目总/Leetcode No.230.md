# Leetcode No.230

###### [230. 二叉搜索树中第K小的元素](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/)

方法一：中序遍历算法

```c++
class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        stack<TreeNode *> stack1;
        while (root != nullptr || stack1.size() > 0) {
            while (root != nullptr) {
                stack1.push(root);
                root = root -> left;
            }
            root = stack1.top();
            stack1.pop();
            k--;
            if (k == 0) {
                break;
            }
            root = root -> right;
        }
        return root -> val;
    }
};
```

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        stack = []
        while root or stack:
            while root:
                stack.append(root)
                root = root.left
            root = stack.pop()
            k -= 1
            if k == 0:
                return root.val
            root = root.right
```

方法二：记录子树的结点数

在方法一中，之所以中序遍历前k个元素，是因为不知道子树的结点数量。所以我们可以记录下以每个结点为根结点的子树的结点数，并查找第k小的值时。使用如下的方法

1. 令node等于根结点，开始搜索

2. 对当前结点node进行如下操作：

   如果node左子树节点数 lefe 小于 k - 1， 则第 k 小的元素一定在 node 的右子树中，令node 等于右子结点，k 等于 k - left - 1, 并继续搜索；

   如果左子树结点数 left 等于 k - 1，则第 k 小的元素即为 node

   如果 node 的左子树结点left 大于 k - 1, 则第 k 小的元素一定在 node 的左子树中，令 node 等于其左子节点搜索。

```c++
class MyBst {
public:
    MyBst(TreeNode *root) {
        this->root = root;
        countNodeNum(root);
    }

    // 返回二叉搜索树中第k小的元素
    int kthSmallest(int k) {
        TreeNode *node = root;
        while (node != nullptr) {
            int left = getNodeNum(node->left);
            if (left < k - 1) {
                node = node->right;
                k -= left + 1;
            } else if (left == k - 1) {
                break;
            } else {
                node = node->left;
            }
        }
        return node->val;
    }

private:
    TreeNode *root;
    unordered_map<TreeNode *, int> nodeNum;

    // 统计以node为根结点的子树的结点数
    int countNodeNum(TreeNode * node) {
        if (node == nullptr) {
            return 0;
        }
        nodeNum[node] = 1 + countNodeNum(node->left) + countNodeNum(node->right);
        return nodeNum[node];
    }

    // 获取以node为根结点的子树的结点数
    int getNodeNum(TreeNode * node) {
        if (node != nullptr && nodeNum.count(node)) {
            return nodeNum[node];
        }else{
            return 0;
        }
    }
};

class Solution {
public:
    int kthSmallest(TreeNode* root, int k) {
        MyBst bst(root);
        return bst.kthSmallest(k);
    }
};

```

