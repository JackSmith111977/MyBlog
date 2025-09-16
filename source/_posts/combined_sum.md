---
title: "组合总和问题"
tags:
    - "算法"
    - "动态规划"
---

# 组合总和问题

## 问题描述
给你一个由 不同 整数组成的数组 nums ，和一个目标整数 target 。请你从 nums 中找出并返回总和为 target 的元素组合的个数。

题目数据保证答案符合 32 位整数范围。

 

示例 1：

输入：nums = [1,2,3], target = 4
输出：7
解释：
所有可能的组合为：
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)
请注意，顺序不同的序列被视作不同的组合。

示例 2：

输入：nums = [9], target = 3
输出：0

 

提示：
1 <= nums.length <= 200
1 <= nums[i] <= 1000
nums 中的所有元素 互不相同
1 <= target <= 1000

## 思路
1. 我们可以将此问题看作爬楼梯问题，如果可以到达某一个target，则最后加的一个数必在nums中
2. 于是，我们可以定义一个数组dp来记录到达每一个小于等于target的数的方法数
3. 将当前状态记为`dp[i]`，即到达i的方法数，而`dp[0] = 1`，因为只有什么都不选一种方法
4. `1 <= i <= target`，只有 `num <= i`，才能判定为可达
5. 因此我们可以递推从 `1 ~ target`，遍历nums判断nums中是否有可达的数，如果有则加上`dp[i - num]`
6. 到此我们可以得到状态转移方程：`dp[i] += dp[i - num]`(每当有num<=i时)，其中num为nums中的数

## 代码实现
根据上述思路，我们可以得到如下代码：
~~~python
class Solution(object):
    def combinationSum4(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: int
        """
        # 定义数组，记录可达的方法数，初始化
        dp = [1] + [0] * target
        # 从1开始递推
        for i in range(1, target + 1):
            # 遍历nums
            for num in nums:
                # 若num可达
                if num <= i:
                    # 累加方法数
                    dp[i] += dp[i - num]

        # 返回可达target的方法数
        return dp[target]
~~~
