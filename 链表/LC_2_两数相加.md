# LeetCode 2. 两数相加

### 1. 题目链接

[LeetCode 2. 两数相加](https://leetcode.cn/problems/add-two-numbers/description/?envType=study-plan-v2&envId=top-100-liked)

### 2. 我的初次思路与遇到的问题

模拟加法，维护进位，同时遍历两条链表；当一条链表结束后，继续处理另一条链表和进位。

### 3. 核心解法：一次循环的模拟加法

**3.1 思路分析**

易错点：
- 两条链表长度不一样时，空节点要按 0 处理。
- 循环结束后可能还剩进位（例如 `9 + 1`），要额外补一个节点。
- 可以把 3 段 `while` 合并为 1 段，逻辑更统一。

使用虚拟头节点 `dummy` 简化构造答案链表。

每轮计算：
- `x = (l1 ? l1->val : 0)`
- `y = (l2 ? l2->val : 0)`
- `sum = x + y + carry`
- 当前位是 `sum % 10`，新的进位是 `sum / 10`

循环条件写成：`l1 != nullptr || l2 != nullptr || carry != 0`，这样可以一次性覆盖所有情况。

**3.2 代码实现 (C++)**

```cpp
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode* dummy = new ListNode(0);
        ListNode* cur = dummy;
        int carry = 0;

        while (l1 != nullptr || l2 != nullptr || carry != 0) {
            int x = (l1 != nullptr) ? l1->val : 0;
            int y = (l2 != nullptr) ? l2->val : 0;
            int sum = x + y + carry;

            carry = sum / 10;
            cur->next = new ListNode(sum % 10);
            cur = cur->next;

            if (l1 != nullptr) l1 = l1->next;
            if (l2 != nullptr) l2 = l2->next;
        }

        ListNode* ans = dummy->next;
        delete dummy;
        return ans;
    }
};
```

### 4. 复杂度分析

- **时间复杂度**: $O(\max(m, n))$，其中 $m, n$ 为两条链表长度。
- **空间复杂度**: $O(1)$（不计答案链表本身）。

### 5. 总结与反思

- 这题是链表“按位处理”的模板题，后续遇到链表数值运算基本都能套用。
