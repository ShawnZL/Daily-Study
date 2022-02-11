# Leetcode No.2

原题目地址[Leetcode No.2](https://leetcode-cn.com/problems/add-two-numbers/)

本题目首先是链表的掌握问题，其次就是最终的进位可能需要再次新建一个**Listnode**

在本题代码中出现了一个**nullptr**，和**NULL**是有区别的，在执行程序时，例如

```c++
#include <iostream>
#include <string>
using namespace std;
void func(int* num)
{
    cout << "this is the ptr function..." << endl;
}

void func(int num)
{
    cout << "this is the normal function..." << endl;
}

void main(int argc, char** argv)
{
    func(NULL);
}
```

在执行时，函数调用的参数为**int**类型，程序自动将**NULL**转换为 **0**。**c++11**中**nullptr**就代表**空指针**，无法转换为数字。

代码：

```c++
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
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* head = nullptr, * tail = nullptr;
        int flag = 0;
        while (l1 || l2) {
            int n1 = l1 ? l1->val : 0;
            int n2 = l2 ? l2->val : 0;
            int sum = n1 + n2 + flag;
            if (!head) {
                head = tail = new ListNode(sum % 10);
            } else {
                tail->next = new ListNode(sum % 10);
                tail = tail->next;
            }
            flag = sum / 10;
            if (l1) {
                l1 = l1->next;
            }
            if (l2) {
                l2 = l2->next;
            }
        }
        if (flag > 0) {
            tail -> next = new ListNode(flag);
        }
        return head;
    }
};
```

