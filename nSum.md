# nSum问题

## 2Sum问题

如果假设输入一个数组`nums`和一个目标和`target`，请你返回`nums`中能够凑出`target`的两个元素的值，比如输入`nums = [1,3,5,6], target = 9`，那么算法返回两个元素`[3,6]`。可以假设只有且仅有一对元素可以凑出`target`。

只需要先对`nums`排序，然后用双指针技巧即可。
```cpp
vector<int> twoSum(vector<int>& nums, int target) {
    // 先对数组排序
    sort(nums.begin(), nums.end());
    // 左右指针
    int lo = 0, hi = nums.size() - 1;
    while (lo < hi) {
        int sum = nums[lo] + nums[hi];
        // 根据 sum 和 target 的比较，移动左右指针
        if (sum < target) {
            lo++;
        } else if (sum > target) {
            hi--;
        } else if (sum == target) {
            return {lo, hi};
        }
    }
    return {};
}
```
当然这是假设只有一对满足要求的情况，如果有多对满足要求的话需要注意，如果`nums`中有重复的数字，则跳过数字时需要跳过所有重复的数字，而不能单纯地对左右指针`+/-1`。
```cpp
vector<vector<int>> twoSumTarget(vector<int>& nums, int target) {
    // nums 数组必须有序
    sort(nums.begin(), nums.end());
    int lo = 0, hi = nums.size() - 1;
    vector<vector<int>> res;
    while (lo < hi) {
        int sum = nums[lo] + nums[hi];
        int left = nums[lo], right = nums[hi];
        if (sum < target) { // 跳过所有相同数字避免重复
            while (lo < hi && nums[lo] == left) lo++;
        } else if (sum > target) {
            while (lo < hi && nums[hi] == right) hi--;
        } else {
            res.push_back({left, right});
            while (lo < hi && nums[lo] == left) lo++;
            while (lo < hi && nums[hi] == right) hi--;
        }
    }
    return res;
}
```

## [三数之和](https://leetcode-cn.com/problems/3sum/)
现在我们想找和为`target`的三个数字，那么对于第一个数字，可能是什么？`nums`中的每一个元素`nums[i]`都有可能！

那么，确定了第一个数字之后，剩下的两个数字可以是什么呢？其实就是和为`target - nums[i]`的两个数字呗，那不就是`twoSum`函数解决的问题么🤔

可以直接写代码了，需要把`twoSum`函数稍作修改即可复用：
```cpp
vector<vector<int>> twoSum(vector<int>& nums, int start, int end, int target) {
    int lo = start, hi = end;
    vector<vector<int>> res;
    while (lo < hi) {
        int sum = nums[lo] + nums[hi];
        int left = nums[lo], right = nums[hi];
        if (sum < target) { // 跳过所有相同数字避免重复
            while (lo < hi && nums[lo] == left) lo++;
        } else if (sum > target) {
            while (lo < hi && nums[hi] == right) hi--;
        } else {
            res.push_back({left, right});
            while (lo < hi && nums[lo] == left) lo++;
            while (lo < hi && nums[hi] == right) hi--;
        }
    }
    return res;
}
vector<vector<int>> threeSum(vector<int>& nums) {
    vector<vector<int>> res;
    int n = nums.size();
    sort(nums.begin(), nums.end());
    for(int i = 0; i < n; ++i) {
        vector<vector<int>> temp = twoSum(nums, i+1, n-1, -nums[i]);
        if(temp.size() != 0) {
            for(vector<int> &tuple: temp) {
                tuple.push_back(nums[i]);
                res.push_back(tuple);
            }
        }
        while(i < n-1 && nums[i] == nums[i+1]) ++i;
    }
    return res;
}
```

## [四数之和](https://leetcode-cn.com/problems/4sum/)
同理，可以确定1个数然后用三数之和的方法遍历剩下的数组。

## nSum问题
如果n为100呢？同样的，我们可以用这样的算法统一一个nSum函数：
```cpp
/* 注意：调用这个函数之前一定要先给 nums 排序 */
vector<vector<int>> nSumTarget(vector<int>& nums, int n, int start, int target) {
    int sz = nums.size();
    vector<vector<int>> res;
    // 至少是 2Sum，且数组大小不应该小于 n
    if (n < 2 || sz < n) return res;
    // 2Sum 是 base case
    if (n == 2) {
        // 双指针那一套操作
        int lo = start, hi = sz - 1;
        while (lo < hi) {
            int sum = nums[lo] + nums[hi];
            int left = nums[lo], right = nums[hi];
            if (sum < target) {
                while (lo < hi && nums[lo] == left) lo++;
            } else if (sum > target) {
                while (lo < hi && nums[hi] == right) hi--;
            } else {
                res.push_back({left, right});
                while (lo < hi && nums[lo] == left) lo++;
                while (lo < hi && nums[hi] == right) hi--;
            }
        }
    } else {
        // n > 2 时，递归计算 (n-1)Sum 的结果
        for (int i = start; i < sz; i++) {
            vector<vector<int>> sub = nSumTarget(nums, n - 1, i + 1, target - nums[i]);
            for (vector<int>& arr : sub) {
                // (n-1)Sum 加上 nums[i] 就是 nSum
                arr.push_back(nums[i]);
                res.push_back(arr);
            }
            while (i < sz - 1 && nums[i] == nums[i + 1]) i++;
        }
    }
    return res;
}
```