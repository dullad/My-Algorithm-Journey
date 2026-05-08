# LeetCode 45. 跳跃游戏 II

### 1. 题目链接
[https://leetcode.cn/problems/jump-game-ii/description/?envType=study-plan-v2&envId=top-100-liked]

### 2. 我的初次思路与遇到的问题 (The Error in "Error Log")
动态规划，dp[i]表示跳到当前下标所需要的最小步数，每个位置遍历其一次跳跃能到达的所有位置（i+nums[i]），更新dp数组。
**易错点：** 动态规划方法存在重复计算，时间复杂度较高。在构思贪心策略时，难以确定增加跳跃次数的准确时机。

### 3. 核心解法：贪心算法（范围分层法）
**3.1 思路分析**
**破局点：** 维护当前跳跃次数下能到达的最远边界 `cur_end`，以及在遍历过程中探明的全局最远边界 `max_reach`。当遍历到达 `cur_end` 时，意味着必须进行下一次跳跃，此时将跳跃次数加一，并将当前边界更新为 `max_reach`。

**3.2 代码实现 (C++)**
```c++ 动态规划
// 完整写出
class Solution {
public:
    int jump(vector<int>& nums) {
        vector<int> dp(nums.size(), INT_MAX);
        dp[0] = 0;
        for(int i = 0; i < nums.size() - 1; i++){
            for(int j = i; j <= i + nums[i] && j < nums.size(); j++){
                dp[j] = min(dp[j], dp[i] + 1);
            }
        }
        return dp[nums.size() - 1];
    }
};
```

```c++
// 看题解复现
class Solution {
public:
    int jump(vector<int>& nums) {
        int jump_times = 0; // 记录跳跃次数
        int cur_end = 0; // 当前跳跃次数下能达到的最远位置
        int max_reach = 0; // 维护遍历过程中发现能到达的最远位置
        for(int i = 0; i < nums.size() - 1; i++){
            max_reach = max(max_reach, i + nums[i]);
            if(i == cur_end){ // 走到当前这一步的极限，需要跳下一次
                jump_times++;
                cur_end = max_reach;
            }
        }
        return jump_times;
    }
};
```

### 4. 复杂度分析
- **时间复杂度**: 贪心算法时间复杂度为 $O(N)$，只需遍历数组一次。动态规划的时间复杂度为 $O(N^2)$。
- **空间复杂度**: 贪心算法空间复杂度为 $O(1)$。

### 5. 总结与反思
此解法结合了类似广度优先搜索（BFS）的分层思想，每一跳代表一层可达范围，通过贪心策略寻找下一层的最大右边界。
