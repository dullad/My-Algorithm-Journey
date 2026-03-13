# LeetCode 240. 搜索二维矩阵 II

### 1. 题目链接

[https://leetcode.cn/problems/search-a-2d-matrix-ii/description/?envType=study-plan-v2&envId=top-100-liked]

### 2. 我的初次思路与遇到的问题 (The Error in "Error Log")

*在这里详细记录你最开始的想法，哪怕是错误的。*

### 3. 核心解法：从左下角/右上角开始搜索 (Z-Search)

**3.1 思路分析**

我们需要找到一个起始点，使得在这个点上，**向一个方向走数值变大，向另一个方向走数值变小**，这样才能根据与 `target` 的大小关系决定下一步怎么走。

-   **左上角/右下角**：向右和向下都变大（或都变小），无法判断方向，排除。
-   **左下角 (Bottom-Left)**：
    -   **往上走**：数值变小。
    -   **往右走**：数值变大。
    -   这正好构成了 BST 的逻辑！

**算法流程（以左下角为例）：**
1.  初始化指针 `row = m-1`, `col = 0`。
2.  循环直到越界：
    -   若 `matrix[row][col] == target`：找到了，返回 `true`。
    -   若 `matrix[row][col] > target`：当前数太大了。因为这一行右边的数都比当前数大，所以目标肯定不在这行，**向上移动一行** (`row--`)。
    -   若 `matrix[row][col] < target`：当前数太小了。因为这一列上面的数都比当前数小，所以目标肯定不在这列，**向右移动一列** (`col++`)。


**3.2 代码实现 (C++)**

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        int m = matrix.size();
        int n = matrix[0].size();
        
        // 从左下角开始搜索
        int row = m - 1, col = 0;
        
        while(row >= 0 && col <= n - 1){
            if(matrix[row][col] == target) return true;
            
            if(matrix[row][col] > target){
                // 当前值比目标大，说明这一行右边的肯定都比目标大
                // 所以排除这一行，向上找
                row--;
            }else{
                // 当前值比目标小，说明这一列上面的肯定都比目标小
                // 所以排除这一列，向右找
                col++;
            }
        } 
        return false;
    }
};
```

### 4. 复杂度分析

- **时间复杂度**: $O(M+N)$。
- **空间复杂度**: $O(1)$。

### 5. 总结与反思

- **起点的选择**：做这类矩阵搜索题，关键在于找到一个具有 “单调性分离” 特征的起点（左下或右上），使得两个移动方向分别对应“变大”和“变小”。