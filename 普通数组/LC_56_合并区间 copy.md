# LeetCode 56.合并区间
### 1. 题目链接

[https://leetcode.cn/problems/merge-intervals/description/?envType=study-plan-v2&envId=top-100-liked]

### 2. 我的初次思路与遇到的问题 (The Error in "Error Log")

*在这里详细记录你最开始的想法，哪怕是错误的。*
先按区间起点顺序排序，确保区间起点大的在后面。维护 `left` 和 `right` 记录当前正在尝试的覆盖区间。
遍历区间集合，对于新区间 `[a, b]`：
*   若 `a > right`，则无法合并，说明当前 `[left, right]` 已经独立，将其放入 `res` 中，并用新区间开启新一轮的合并；
*   若 `b <= right`，说明新区间完全被当前覆盖区间包含，可以直接跳过；
*   其他情况（即区间有交集且超出了当前右边界），则用新区间的 `b` 去更新 `right`。
最终遍历完不要忘了将最后一个还没加入的 `[left, right]` 加入 `res` 中。
*主要难点*：在于 C++ 中怎么把二维数组自定义排序写好（API 和 Lambda 表达式的用法），以及理清合并时各种重叠情况的边界条件。


### 3. 核心解法：排序

**3.1 思路分析**

如果不排序，区间是杂乱无章的，判断重叠会非常复杂。**当我们遇到区间问题时，第一反应应该是“排序”**。通过按左端点升序排列，我们可以确保所有可能重叠的区间在数组中都是连续的。

1.  **按左端点排序**：
    将区间集合按照每个区间的左端点从小到大进行排序。这样，对于任意遍历到的区间，它的左端点一定大于等于前面处理过的所有区间的左端点。

2.  **贪心合并**：
    我们维护一个“当前正在合并的区间” `[left, right]`（初始为排序后的第一个区间）。
    然后依次遍历后续的区间 `intervals[i]`：
    *   **完全包含**：如果 `intervals[i][1] <= right`，说明当前区间被整个包住了，不需要任何操作，继续下一个。
    *   **无交集**：如果 `intervals[i][0] > right`，说明新区间和我们正在维护的区间完全断开。这时我们把维护好的 `[left, right]` 收集到答案数组中，并把焦点转移到新区间，即更新 `left = intervals[i][0]`, `right = intervals[i][1]`。
    *   **有交集需要延展**：如果左端点有交集，且右端点伸出去了，那就只更新右边界 `right = intervals[i][1]` 即可。

3.  **收尾工作**：
    由于我们的逻辑是“遇到断开的区间时，才把上一个区间存入答案”，所以遍历结束时，最后维护的那个 `[left, right]` 是没有触发存入操作的，需要在循环结束后手动 `push_back` 一次。



**3.2 代码实现 (C++)**

```c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        int n = intervals.size();
        if(n <= 1) return intervals;
        sort(intervals.begin(), intervals.end(), [](const vector<int>& a, const vector<int>& b) {
            return a[0] < b[0];
        });
        vector<vector<int>> res;
        int left = intervals[0][0], right = intervals[0][1];
        for(int i = 1; i < n; i++){
            if(intervals[i][1] <= right){
                continue;
            }else if(intervals[i][0] >right){ // 不重叠，开新的
                res.push_back({left, right});
                left = intervals[i][0];
                right = intervals[i][1];
            }else{
                right = intervals[i][1];
            }
        }
        res.push_back({left, right});
        return res;
    }
};
```

### 4. 复杂度分析

- **时间复杂度**: $O(NlogN)$。
- **空间复杂度**: $O(logN)$。

### 5. 总结与反思

- **区间问题--排序**：有时候排序，先固定一端，可以极大简化后续判断的分类讨论。
- **C++中的自定义排序**：学会使用`[](const vector<int>& a, const vector<int>& b) { return a[0] < b[0]; }`这样的匿名函数，这在各类需要按某种特定规则排序的题目中实用且代码简洁。。
