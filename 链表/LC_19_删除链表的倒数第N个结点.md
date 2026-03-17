# LeetCode 19. 删除链表的倒数第 N 个结点

### 1. 题目链接

[LeetCode 19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/description/?envType=study-plan-v2&envId=top-100-liked)

### 2. 我的初次思路与遇到的问题

初次想法是快慢指针：快指针先走 `n` 步，再和慢指针一起走，当快指针到末尾时，慢指针正好在待删节点前一位。

### 3. 核心解法：快慢指针 + 虚拟头节点

**3.1 思路分析**

1. 建立 `dummy->next = head`，`fast`、`slow` 都从 `dummy` 出发。
2. 先让 `fast` 走 `n` 步，拉开距离。
3. 然后 `fast` 与 `slow` 同时前进，直到 `fast->next == nullptr`。
4. 此时 `slow->next` 就是要删除的节点，执行跳过。

**3.2 代码实现 (C++)**

```cpp
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode* dummy = new ListNode(0);
        dummy->next = head;

        ListNode* fast = dummy;
        ListNode* slow = dummy;

        while (n--) {
            fast = fast->next;
        }

        while (fast != nullptr && fast->next != nullptr) {
            fast = fast->next;
            slow = slow->next;
        }

        ListNode* toDelete = slow->next;
        slow->next = slow->next->next;
        delete toDelete;

        ListNode* ans = dummy->next;
        delete dummy;
        return ans;
    }
};
```

### 4. 复杂度分析

- **时间复杂度**: $O(n)$。
- **空间复杂度**: $O(1)$。

### 5. 总结与反思

- 只要题目涉及删除头节点可能性，优先上 `dummy`，能大幅减少边界判断。
