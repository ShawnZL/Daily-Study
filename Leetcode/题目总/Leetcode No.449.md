# Leetcode No.449

#### [449. 序列化和反序列化二叉搜索树](https://leetcode.cn/problems/serialize-and-deserialize-bst/)

```
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Codec {
public:

    void preOrder(TreeNode* root, vector<int>& res) {
        if (!root) return;
        res.push_back(root->val);
        preOrder(root->left, res);
        preOrder(root->right, res);
    }

    string vector2string(vector<int>& vals) {
        string res;
        if (vals.empty()) return res;
        for (int i = 0; i < vals.size() - 1; ++i) {
            res += to_string(vals[i]) + ",";
        }
        res += to_string(vals[vals.size() - 1]);
        return res;
    }

    vector<int> split(string& s) {
        vector<int> res;
        size_t pos = 0;
        std::string token;
        while ((pos = s.find(",")) != std::string::npos) {
            token = s.substr(0, pos);
            res.push_back(stoi(token));
            s.erase(0, pos + 1);
        }
        res.push_back(stoi(s));
        return res;
    }

    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        vector<int> vals;
        preOrder(root, vals);
        return vector2string(vals);
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        if (data.empty()) return nullptr;
        vector<int> vals = split(data);
        TreeNode* root = new TreeNode(vals[0]);
        vector<int> leftVals;
        vector<int> rightVals;
        for (int val : vals) {
            if (val < vals[0]) {
                leftVals.push_back(val);
            } else if (val > vals[0]) {
                rightVals.push_back(val);
            }
        }
        root->left = deserialize(vector2string(leftVals));
        root->right = deserialize(vector2string(rightVals));
        return root;
    }
};

// Your Codec object will be instantiated and called as such:
// Codec* ser = new Codec();
// Codec* deser = new Codec();
// string tree = ser->serialize(root);
// TreeNode* ans = deser->deserialize(tree);
// return ans;
```

