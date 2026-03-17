# LeetCode 25. K 个一组翻转链表

### 1. 题目链接

[LeetCode 25. K 个一组翻转链表](https://leetcode.cn/problems/reverse-nodes-in-k-group/description/?envType=study-plan-v2&envId=top-100-liked)

### 2. 我的初次思路与遇到的问题

原思路是先判断是否够 `k` 个节点，再对这一段执行反转，最后推进指针。
关键坑点在于：翻转后，`last` 已经不是当前组新的尾后关系的可靠定位，`cur = last` 会错位。应让 `cur` 前进 `k` 步到“新尾节点”。

### 3. 核心解法

**3.1 思路分析**

整体采用“分组检查 + 原地反转”：

1. `cur` 始终指向“当前待翻转分组的前一个节点”（组前驱）。
2. 调用 `canFlip(cur, k)` 检查从 `cur->next` 开始是否有完整 `k` 个节点：
   - 有：返回该组末尾节点 `last`
   - 无：返回 `nullptr`，结束循环
3. 对 `[cur->next, last]` 这段执行原地反转（`flipNextK(cur, last)`）。
4. 反转后，`cur` 需要移动到“这一组反转后的尾节点”，即向前走 `k` 步。
5. 重复步骤 2，直到剩余节点不足 `k`。

这个写法的本质是：**每轮只处理一段长度恰好为 `k` 的闭区间，且不额外开数组或栈。**

**3.2 代码实现 (C++)**

```cpp
class Solution {
    ListNode* canFlip(ListNode* cur, int k){
        while(k--){
            cur = cur->next;
            if(cur == nullptr){
                return nullptr;
            }
        }
        return cur;
        
    }
    void flipNextK(ListNode* cur, ListNode* last){
        ListNode* prev = last->next;
        ListNode* curH = cur->next;
        while(prev != last){
            ListNode* next_node = curH->next; //暂存后继
            curH->next = prev; //反转curH指向
            prev = curH;  //prev后移，成为新的反转链表的头部
            curH = next_node;  //curH后移，移到新一个没反转的位置
        }
        cur->next = prev;
    }
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        if(k == 1) return head;
        ListNode* dummyHead = new ListNode();
        dummyHead->next = head;
        ListNode* cur = dummyHead;

        ListNode* last = canFlip(cur, k);
        while(last != nullptr){
            flipNextK(cur, last);//反转cur->next到last的k个节点
            // cur = last; 原代码cur=last是错的！因为last指向的是翻转前的。
            for (int i = 0; i < k; ++i) {
                cur = cur->next;
            }
            last = canFlip(cur, k);
        }
        return dummyHead->next;
    }
};
```

### 4. 复杂度分析

- **时间复杂度**: $O(n)$。
- **空间复杂度**: $O(1)$。

### 5. 总结与反思

- 思路：`检查够K -> 组内反转 -> cur前进k步 -> 继续下一组`。
