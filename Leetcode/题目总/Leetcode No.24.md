# Leetcode No.24

#### [24. 两两交换链表中的节点](https://leetcode.cn/problems/swap-nodes-in-pairs/)

```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (head == nullptr || head -> next == nullptr) return head;
        ListNode * dummyHead = new ListNode(0);
        dummyHead -> next = head;
        ListNode * temp = dummyHead;
        while (temp -> next != nullptr && temp -> next -> next != nullptr) {
            ListNode * node1 = temp -> next;
            ListNode * node2 = temp -> next -> next;
            temp -> next = node2;
            node1 -> next = node2 -> next; //不能断
            node2 -> next = node1;
            temp = node1;
        }
        return dummyHead -> next;
    }
};
```

