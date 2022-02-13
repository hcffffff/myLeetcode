# 动态规划笔记

## 定义与说明

首先，动态规划问题的一般形式就是求最值。动态规划其实是运筹学的一种最优化方法，只不过在计算机问题上应用比较多，比如说让你求最长递增子序列呀，最小编辑距离呀等等。

既然是要求最值，核心问题是穷举。因为要求最值，肯定要把所有可行的答案穷举出来，然后在其中找最值。

动态规划的穷举有点特别，因为这类问题存在**重叠子问题**，如果暴力穷举的话效率会极其低下，所以需要「备忘录」或者「DP table」来优化穷举过程，避免不必要的计算。

而且，动态规划问题一定会具备**最优子结构**，才能通过子问题的最值得到原问题的最值。

另外，虽然动态规划的核心思想就是穷举求最值，但是问题可以千变万化，穷举所有可行解其实并不是一件容易的事，只有列出正确的**状态转移方程**，才能正确地穷举。

以上提到的重叠子问题、最优子结构、状态转移方程就是动态规划三要素。具体什么意思等会会举例详解，但是在实际的算法问题中，写出状态转移方程是最困难的，这也就是为什么很多朋友觉得动态规划问题困难的原因。

牢记：**明确 base case -> 明确「状态」-> 明确「选择」 -> 定义 dp 数组/函数的含义**。用伪代码表示：
```cpp
# 初始化 base case
dp[0][0][...] = base
# 进行状态转移
for 状态1 in 状态1的所有取值：
    for 状态2 in 状态2的所有取值：
        for ...
            dp[状态1][状态2][...] = 求最值(选择1，选择2...)
```

## 简单的斐波那契数列
用一个dp数组作为记录，从底向上进行计算
```cpp
int fib(int N) {
    if (N == 0) return 0;
    int[] dp = new int[N + 1];
    // base case
    dp[0] = 0; dp[1] = 1;
    // 状态转移
    for (int i = 2; i <= N; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }

    return dp[N];
}
```
容易发现该问题的状态转移方程为：$f(n) = f(n-1) + f(n-2)$，basecase为$f(1)=1, f(2)=1$。

优化：实际上不需要一个数组来存储所有中间结果，因此可以将空间复杂度降为O(1)。

## [凑零钱问题](https://leetcode-cn.com/problems/coin-change/)
从上往下，每次从amount中减去一个coin，result+1，类似暴力，会超过时间限制。边界条件为`dp(0)=0, dp(n)=-1 n<0`

暴力解法，时间复杂度为指数级别：
```cpp
int coinChange(vector<int>& coins, int amount) {
    return dp(coins, amount);
}
int dp(vector<int>& coins, int amount) {
    if(amount < 0) return -1; // Baseline
    if(amount == 0) return 0; // Baseline
    int res = 1e6;
    for(auto & coin: coins) { // 暴力
        int sub = dp(coins, amount - coin);
        if(sub == -1) continue;
        res = min(sub + 1, res);
    }
    return res==1e6 ? -1 : res;
}
```

带备忘录的dp，时间复杂度为O(n)：
```cpp
int coinChange(vector<int>& coins, int amount) {
    vector<int> memo(amount+1, -2);
    return dp(coins, amount, memo);
}
int dp(vector<int>& coins, int amount, vector<int>& memo) {
    if(amount < 0) return -1;
    if(amount == 0) return 0;
    if (memo[amount] != -2) // 如果备忘录中存在运算过的结果，则直接返回
        return memo[amount];
    int res = 1e6;
    for(auto & coin: coins) {
        int sub = dp(coins, amount - coin, memo);
        if(sub == -1) continue;
        res = min(sub + 1, res);
    }
    memo[amount] = res==1e6 ? -1 : res;
    return res==1e6 ? -1 : res;
}
```

实际上从底向上的推导会更简洁：
```cpp
int coinChange(vector<int>& coins, int amount) {
    vector<int> dp(amount+1, amount+1); // 保存所有中间结果，初始化为amount+1是因为因为凑成 amount 金额的硬币数最多只可能等于 amount
    dp[0] = 0; // Baseline
    for(int i = 0; i < amount+1; ++i) {
        for(auto& coin: coins) {
            if(i - coin < 0) continue;
            dp[i] = min(dp[i-coin] + 1, dp[i]);
        }
    }
    return dp[amount] == amount+1 ? -1 : dp[amount];
}
```

### 相似题目：
[最低票价，2021交大](https://leetcode-cn.com/problems/minimum-cost-for-tickets/)
先上车，后买票
```cpp
int mincostTickets(vector<int>& days, vector<int>& costs) {
    int last = days[days.size() - 1];
    vector<int> memo(last + 1, 0);
    int idx = 0; // 用于记录需要旅行的天
    for(int i = 1; i <= last; ++i) {
        if(i == days[idx]) {
            int cost = 1e6;
            int onedayAgo = i - 1;
            int oneweekAgo = i - 7 > 0 ? i - 7 : 0;
            int onemonthAgo = i - 30 > 0 ? i - 30 : 0;

            cost = min(memo[onedayAgo] + costs[0], cost);
            cost = min(memo[oneweekAgo] + costs[1], cost);
            cost = min(memo[onemonthAgo] + costs[2], cost);
            
            memo[i] = cost;
            idx++;
        } else { // 若不需要旅行，则直接等于前一天的cost
            memo[i] = memo[i-1];
        }
    }
    return memo[last];
}
```