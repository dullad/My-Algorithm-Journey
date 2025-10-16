# LeetCode 53. 最大子数组和 (Maximum Subarray)

### 1. 题目链接

[https://leetcode.cn/problems/maximum-subarray/](https://leetcode.cn/problems/maximum-subarray/)

### 2. 我的初次思路与遇到的问题 (The Error in "Error Log")

*在这里详细记录你最开始的想法，哪怕是错误的。*

例如：
我一开始想用暴力法，双重循环遍历所有子数组，计算和并找到最大值。但很快意识到时间复杂度是 $O(n^2)$，对于 $10^5$ 的数据量肯定会超时。
然后我尝试用滑动窗口，但发现窗口的收缩条件很难确定，因为子数组中可能包含负数，窗口扩大不一定和就增大。

*（这个部分是整个错题本最有价值的地方，它记录了你的思维误区。）*

### 3. 核心解法：动态规划

**3.1 思路分析**

这道题是典型的动态规划问题。关键在于定义 `dp[i]` 的含义。

- **状态定义**: `dp[i]` 表示以 `nums[i]` **结尾** 的 "连续子数组的最大和"。
- **状态转移方程**:
  当我们计算 `dp[i]` 时，`nums[i]` 必须被包含。此时有两种选择：
    1. `nums[i]` 自己独立成一个子数组，最大和就是 `nums[i]`。
    2. `nums[i]` 加入到前面以 `nums[i-1]` 结尾的子数组中，形成更大的子数组。最大和是 `dp[i-1] + nums[i]`。
  因此，`dp[i]` 应该取这两者中的较大值：`dp[i] = max(nums[i], dp[i-1] + nums[i])`。
- **最终结果**: 问题的解并不是 `dp[n-1]`，因为最大子数组不一定以最后一个元素结尾。我们需要遍历整个 `dp` 数组，找到其中的最大值。

**3.2 代码实现 (Python)**

```python
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        if not nums:
            return 0
        
        # dp[i] 表示以 nums[i] 结尾的连续子数组的最大和
        # 由于 dp[i] 只与 dp[i-1] 有关，可以用一个变量进行状态压缩
        
        # pre_max 相当于 dp[i-1]
        pre_max = nums[0]
        # result 用于记录全局最大值
        result = nums[0]
        
        for i in range(1, len(nums)):
            current_max = max(nums[i], pre_max + nums[i])
            result = max(result, current_max)
            pre_max = current_max
            
        return result
```

### 4. 复杂度分析

- **时间复杂度**: $O(n)$，因为我们只遍历了一次数组。
- **空间复杂度**: $O(1)$，因为我们使用了状态压缩，只用了常数个变量。如果使用DP数组，空间复杂度为 $O(n)$。

### 5. 总结与反思

- 这道题的核心是**局部最优和全局最优**的关系。`dp[i]` 是局部最优解，而我们维护的 `result` 是全局最优解。
- 状态定义是动态规划的灵魂。"以 `nums[i]` 结尾" 这个定义非常关键，保证了子数组的连续性。
- 状态压缩是优化DP空间复杂度的常用技巧，当 `dp[i]` 只与前几个状态有关时，可以考虑使用。