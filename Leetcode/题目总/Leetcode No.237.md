# Leetcode No.237

###### [237. 删除链表中的节点](https://leetcode-cn.com/problems/delete-node-in-a-linked-list/)

方法一：本题给与的结点就是要删除掉的结点，所以需要我们考虑直接对于给与的结点进行操作。

交换要删除结点的和之后结点的数值，直接将要删除结点后一个删除

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    void deleteNode(ListNode* node) {
        node -> val = node -> next -> val;
        node -> next = node -> next -> next;
    }
};
```

