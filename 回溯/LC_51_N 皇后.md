# LeetCode 51. N 皇后

### 1. 题目链接
[51. N 皇后](https://leetcode.cn/problems/n-queens/description/?envType=study-plan-v2&envId=top-100-liked)

### 2. 存在的问题与优化思路
一开始用一个和棋盘等大的二维 flag 数组来记录棋盘上可以下棋的位置，每次拷贝传递，极为低效。
**优化：** 实际上三个一维数组就可以实现 O(1) 的冲突校验。`col(n)` 记录可以下的列，`diag_1(2n-1)` 记录左上到右下的主对角线，`diag_2(2n-1)` 记录右上到左下的副对角线。只有三个方向都没被占时，目标位置才能下 `Q`。需要理清对角线序号与棋盘坐标 `(row, col)` 的数学映射关系。

### 3. 核心解法：带状态压缩的一维回溯

**3.1 思路分析**
N皇后是一个典型的受限排列问题。我们让**树的深度 `row` 控制行数**（每行只放一个，天然避免同行冲突），让**树的广度 `i` 控制列数**（尝试当前行的每一列）。
- **列冲突 (`col`)**：同列坐标 `i` 相等。
- **主对角线冲突 (`diag_1`)**：同一主对角线上的点满足 `row - col = 常数`。 `n - 1 - row + i`。
- **副对角线冲突 (`diag_2`)**：同一副对角线上的点满足 `row + col = 常数`。 `row + i`。

**3.2 代码实现 (C++)**
```cpp
class Solution {
    vector<vector<string>> res;
    vector<string> solution;
    
    // 快速生成一行
    string getQline(int n, int row){
        string line(n, '.');
        line[row] = 'Q';
        return line;
    }
    
    void backtrack(vector<bool>& col, vector<bool>& diag_1, vector<bool>& diag_2, int n, int row){
        // 深度：当前已成功摆放到第 row 行，如果 row == n 说明全下完了
        if(row == n){
            res.push_back(solution);
            return;
        }
        // 广度：遍历该行的每一列 i
        for(int i = 0; i < n; i++){
            // 校验：列、主对角线、副对角线都可用 (对应的 flag 为 true)
            if(col[i] && diag_1[n - 1 - row + i] && diag_2[row + i]){
                solution.push_back(getQline(n, i));
                
                // 标记为占用
                col[i] = diag_1[n - 1 - row + i] = diag_2[row + i] = false;
                
                // 递归进入下一行
                backtrack(col, diag_1, diag_2, n, row + 1);
                
                // 回溯恢复现场，出栈试下一个位置
                col[i] = diag_1[n - 1 - row + i] = diag_2[row + i] = true;
                solution.pop_back();
            }
        }
    }
public:
    vector<vector<string>> solveNQueens(int n) {
        // true表示该列/对角线空闲可以放
        vector<bool> col(n, true), diag_1(n * 2 - 1, true), diag_2(n * 2 - 1, true);
        backtrack(col, diag_1, diag_2, n, 0);
        return res;
    }
};
```

### 4. 复杂度分析
- **时间复杂度**: `$O(N!)$`。实际由于严格的剪枝，有效搜索空间远小于 `N!`，单次放置 `Q` 及判别的代价均在优化的辅助数组帮助下做到了 `$O(1)$`。
- **空间复杂度**: `$O(N)$`。一维布尔数组和递归调用栈的最大深度均为 N，抛开用于收录答案的 `res`，主要开销随 N 线性增长。

### 5. 总结与反思
- **二维转一维技巧**：凡是在网格上遇到“米字型”、“对角线”校验，切忌去二维循环里死找。利用**坐标和差值恒定**的几何性质，用一维哈希表/数组作 `$O(1)$` 哨兵。
- **回溯模板对应**：`row` 控制递归深度（天然规避同行冲突），`i` 控制遍历宽度（列校验）。这就是最正宗的“按行下棋法”。
