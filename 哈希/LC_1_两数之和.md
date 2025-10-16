# LeetCode 1. 两数之和

### 1. 题目链接

[https://leetcode.cn/problems/two-sum/?envType=study-plan-v2&envId=top-100-liked]

### 2. 我的初次思路与遇到的问题 (The Error in "Error Log")

*在这里详细记录你最开始的想法，哪怕是错误的。*

我一开始想用暴力法，遍历所有两数之和，但是时间复杂度是 $O(n^2)$。我开始思考有没有能够降低时间复杂度的算法。

我开始思考建立一张哈希表，记录nums中出现的每个数字，这样只需要遍历一次数组，去查`target-nums[i]`这个值是否存在，即可快速返回结果。哈希表每个数字对应的值为其下标（初始化为-1即可）。但是这样需要一个超级大的数组。

可能需要重温以下哈希表，不是只有数组可以作为哈希表，有专门的哈希表数据结构，这样只需要出现一个值插入一个值即可，避免浪费空间。

### 3. 核心解法：动态规划

**3.1 思路分析**

这道题思路其实非常简单，就是建哈希表，然后遍历数组查表即可，哈希表的值就是对应键的数组下标。关键就是哈希表数据结构的问题。哈希表使用一般是`unordered_map<int,int>m;`，参考以下代码片段：

```C++
#include <unordered_map>
int main(){
	std::unordered_map<std::string, int> mymap;
	//插入
	myMap["apple"] = 3;
    myMap.insert({"banana", 5});
    //查找
    if(mymap.find("banana") != myMap.end()){
        std::cout << "Banana found" << std::endl;
    }
    //删除
    myMap.erase("apple");
    //遍历
    for (const auto& pair : myMap) {
        std::cout << pair.first << ": " << pair.second << std::endl;
    }
}
```

**3.2 代码实现 (Python)**

```c++
//第一次错误尝试
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int,int>map;
        for(int i = 0; i < nums.size(); i++){
            map[nums[i]] = i;
        }
        for(int i = 0; i < nums.size(); i++){
            int j = target - nums[i];
            if(i != map[j] && map.find(j) != map.end()){
                return {i, map[j]};
            }
        }
        return {};
    }
};
```
以上代码存在bug，因为可能数组中会存在重复元素，下标会被覆盖。
此外，在`if(i != map[j] && map.find(j) != map.end())`中，如果j不存在，则`map[j]`会访问一个不存在的键，会默认插入一个新元素，效率低下且会导致逻辑错误。
正确做法应该是一次遍历，将两个循环合并为一个。边遍历，边检查补数是否在map中，如果不在，就将当前数字和索引存入map。如果在，则返回解。



### 4. 复杂度分析

- **时间复杂度**: $O(n)$，因为我们只遍历了一次数组。
- **空间复杂度**: $O(1)$，因为我们使用了状态压缩，只用了常数个变量。如果使用DP数组，空间复杂度为 $O(n)$。

### 5. 总结与反思

- 这道题的核心是**局部最优和全局最优**的关系。`dp[i]` 是局部最优解，而我们维护的 `result` 是全局最优解。
- 状态定义是动态规划的灵魂。"以 `nums[i]` 结尾" 这个定义非常关键，保证了子数组的连续性。
- 状态压缩是优化DP空间复杂度的常用技巧，当 `dp[i]` 只与前几个状态有关时，可以考虑使用。