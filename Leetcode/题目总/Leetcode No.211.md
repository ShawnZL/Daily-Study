# Leetcode No.211

###### [211. 添加与搜索单词 - 数据结构设计](https://leetcode-cn.com/problems/design-add-and-search-words-data-structure/)

方法一：字典树

字典树（前缀树）是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。前缀树可以用O(S) 的时间完成插入和查询操作

```c++
struct TrieNode {
    vector<TrieNode*> child;
    bool isEnd;
    TrieNode() {
        this->child = vector<TrieNode*>(26, nullptr);
        this->isEnd = false;
    }
};

void insert(TrieNode* root, const string& word) {
    TrieNode* node = root;
    for (auto c: word) {
        if (node -> child[c - 'a'] == nullptr) {
            node -> child[c - 'a'] = new TrieNode();
        }
        node = node -> child[c - 'a'];
    }
    node -> isEnd = true;
}

class WordDictionary {
public:
    WordDictionary() {
       trie = new TrieNode(); 
    }
    
    void addWord(string word) {
        insert(trie, word);
    }
    
    bool dfs(const string &word, int index, TrieNode* node) {
        if (index == word.size()) {
            return node -> isEnd;
        }
        char ch = word[index];
        if (ch >= 'a' && ch <= 'z') {
            TrieNode* child = node -> child[ch - 'a'];
            if (child != nullptr && dfs(word, index + 1, child)) {
                return true;
            }
        } else if (ch == '.') {
            for (int i = 0; i < 26; i++) {
                TrieNode* next = node -> child[i];
                if (next != nullptr && dfs(word, index + 1, next)) {
                    return true;
                }
            }
        }
        return false;
    }

    bool search(string word) {
        return dfs(word, 0, trie);
    }
private:
    TrieNode* trie;
};

/**
 * Your WordDictionary object will be instantiated and called as such:
 * WordDictionary* obj = new WordDictionary();
 * obj->addWord(word);
 * bool param_2 = obj->search(word);
 */
```



