# 回溯算法笔记

## 定义与说明

回溯算法其实就是我们常说的 DFS 算法，本质上就是一种暴力穷举算法。

解决一个回溯问题，实际上就是一个决策树的遍历过程。你只需要思考 3 个问题：

1. 路径：也就是已经做出的选择。
2. 选择列表：也就是你当前可以做的选择。
3. 结束条件：也就是到达决策树底层，无法再做选择的条件。

一般的：
```python
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        # 做选择
        将该选择从选择列表移除
        路径.add(选择)
        backtrack(路径, 选择列表)
        # 撤销选择
        路径.remove(选择)
        将该选择再加入选择列表
```

## [全排列问题](https://leetcode-cn.com/problems/permutations/)
```cpp
void permute1(vector<vector<int>>& ans, vector<int>& nums, int start, int end){
    //递归
    for(int j = start; j <= end; ++j){
        swap(nums[start], nums[j]);
        permute1(ans, nums, start+1, end);//start+1
        swap(nums[start], nums[j]);
    }
    if(start == end) ans.push_back(nums);
}
```

## [N皇后问题](https://leetcode-cn.com/problems/n-queens/)
```cpp
void backtracking(vector<string>& chessboard, int row,int n)
{
    if (row >= n) // 递归结束
    {
        results.push_back(chessboard);
        return;
    }
    for (int col = 0; col < n; col++)
    {
        if (isvalid(row, col, n, chessboard))
        {
            chessboard[row][col] = 'Q';
        }
        else
            continue;
        backtracking(chessboard, row + 1, n); // 递归深度加1，行就要加1
        chessboard[row][col] = '.';
    }
}
```