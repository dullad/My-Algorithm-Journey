# LeetCode 437. 路径总和 III

### 1. 题目链接

[LeetCode 437. 路径总和 III](https://leetcode.cn/problems/path-sum-iii/description/?envType=study-plan-v2&envId=top-100-liked)

### 2. 我的初次思路与遇到的问题
```cpp
class Solution {
public:
    int pathSum(TreeNode* root, int targetSum) {
        if(!root) return 0;
        if(root->val == targetSum) return 1;
        return pathSum(root->left, targetSum) + pathSum(root->right, targetSum) + pathSum(root->left, targetSum-root->val) + pathSum(root->right, targetSum-root->val);
    }
};
```
- 直接尝试在一重递归内搞定所有的判断，混淆了函数核心功能。这把“必须包含当前节点进行加减处理的情况”与“不论是否包括单纯往底层探路径全局答案累加的情况”揉入了一个入口，产生了极多的重复计数和路线断档的情况。

### 3. 核心解法：双层 DFS

**思路分析**
- 我们可以把“不管是否包括当前节点的所有合法路径”这个逻辑拆分为内外两层任务彻底解耦：
   1. 设计子函数 `dfs(root, target)` 作为内层：它的功能明确为**必须**在这棵树上寻找以 `root` 为起始起点的、所有能连成目标值的条数。
   2. 主函数 `pathSum(root, target)` 外层总览：答案其实应归结为 以自己当头的数量 `dfs(root)`，加上它左右侧子孩子作为头的数量 `pathSum(left)`与 `pathSum(right)`的和。
- 这里需要注意测试数据在一直加减累计的时候很容易会超 `int` ，所以必须要在内层函数 `dfs` 控制入参为 `long long targetSum`。

**代码实现 (C++)**

```cpp
class Solution {
    int dfs(TreeNode* root, long long targetSum){ // 注意使用long long，因为中间过程中sum可能超越int大小
        // 确定必须强制以当前的root节点当做起点开头的路径匹配数。
        if(!root) return 0;
        int cnt = 0;
        if(root->val == targetSum) cnt++;
        // 哪怕当前符合了也不要return结束，因为下面可能加一下一个正数又减一个负数再次绕回 target
        cnt += dfs(root->left, targetSum - root->val);
        cnt += dfs(root->right, targetSum - root->val);
        return cnt;
    }
public:
    int pathSum(TreeNode* root, int targetSum) {
        if(!root) return 0;
        // 把自己当做起点的结果数量 + 彻底略过自己让左右两边各自做起点的数量
        return dfs(root, targetSum) + pathSum(root->left, targetSum) + pathSum(root->right, targetSum);
    }
};
```

### 4. 复杂度分析

- **时间复杂度**: $O(N^2)$。外层对于每一个树上的节点都会进入作为一次内层 `dfs` 的起始原点，而在内部最坏情况下退化为单侧链表又扫描全体。如果二叉树完美平衡，可达 $O(N \log N)$。
- **空间复杂度**: $O(H)$。递归回溯压栈受限取决于深度的最高节点高。

### 5. 总结与反思

- 绝不能在一个 DFS 函数兼顾“作为起点延续”和“直接另起炉灶”两条交叉线，这样会引起状态的极度失控。“双重遍历内套”是最直接纯粹的设计解决。
