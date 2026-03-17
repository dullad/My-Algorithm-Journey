# LeetCode 23. 合并 K 个升序链表

### 1. 题目链接

[LeetCode 23. 合并 K 个升序链表](https://leetcode.cn/problems/merge-k-sorted-lists/description/?envType=study-plan-v2&envId=top-100-liked)

### 2. 我的初次思路与遇到的问题

初次写法是顺序两两合并：先把 `lists[0]` 与 `lists[1]` 合并，再与 `lists[2]` 合并……

问题在于：每次都在处理越来越长的链表，整体复杂度偏高，最坏可达 $O(NK^2)$（$N$ 是平均每条链长度，$K$ 是链表条数）。

### 3. 核心解法

#### 3.1 分治合并（推荐掌握）

思路与归并排序一致：
- 把 `K` 条链表不断二分。
- 递归到底后，两两合并。
- 每个节点在每一层最多参与一次合并，共 $\log K$ 层。

**代码实现 (C++)：分治**

```cpp
class Solution {
    ListNode* mergeTwo(ListNode* a, ListNode* b) {
        ListNode* dummy_head = new ListNode();
        ListNode* cur = dummy_head;

        while (a != nullptr && b != nullptr) {
            if (a->val < b->val) {
                cur->next = a;
                a = a->next;
            } else {
                cur->next = b;
                b = b->next;
            }
            cur = cur->next;
        }
        cur->next = (a != nullptr) ? a : b;
        return dummy_head->next;
    }

    ListNode* mergeRange(vector<ListNode*>& lists, int l, int r) {
        if (l > r) return nullptr;
        if (l == r) return lists[l];

        int mid = l + (r - l) / 2;
        ListNode* left = mergeRange(lists, l, mid);
        ListNode* right = mergeRange(lists, mid + 1, r);
        return mergeTwo(left, right);
    }

public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        return mergeRange(lists, 0, lists.size() - 1);
    }
};
```

### 4. 复杂度分析

- **时间复杂度** 时间复杂度都为 $O(NK\log K)$。
- **空间复杂度** 额外空间：递归栈 $O(\log K)$。

### 5. 总结与反思

