---
title: "二分法查找"
date: 2025/09/14 16:32:00
tags: 
    - 算法
    - 二分法
categories:
    - 算法笔记
---

# 二分法查找

## 题目描述
在排序数组中查找元素的第一个和最后一个位置

给你一个按照非递减顺序排列的整数数组 nums，和一个目标值 target。请你找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 target，返回 [-1, -1]。

你必须设计并实现时间复杂度为 O(log n) 的算法解决此问题。

 

示例 1：

输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]

示例 2：

输入：nums = [5,7,7,8,8,10], target = 6
输出：[-1,-1]

示例 3：

输入：nums = [], target = 0
输出：[-1,-1]

### 思路
1. 我们可以用二分法查找来降低时间复杂度
2. 二分查找的三种思路：
   1. 闭区间：[left, right]
   初始值：
       * left = 0, right = n - 1
       * mid = (left + right) // 2
   * 当 left <= right 时，循环执行
   * 若 nums[mid] < target: left = mid + 1
   否则 right = mid - 1
   * 这样，循环结束后，若target在数组中，则left在right + 1的位置，当num[mid] == target时，执行 right = mid - 1,而target值索引即为left，故最开始出现的位置为left
   * 因此返回值left
    ~~~python
    def lower_bound(nums: List[int], target: int) -> int:
        """
        二分法查找某数字最早出现的位置
        """
        l = 0
        r = len(nums) - 1
        while l <= r: # 闭区间[l, r]
            m = (l + r) // 2
            if nums[m] < target:
                l = m + 1 # [m + 1, r]
            else:
                r = m - 1 # [l, m - 1]
        return l # 若没有则会停在-1或len(nums)
    ~~~
   2. 左闭右开区间：[left, right)
    初始值：
      * left = 0, right = n
      * mid = (left + right) // 2
   * 当 left < right 时，循环执行
   * 若 nums[mid] < target: left = mid + 1
    否则 right = mid
   * 这样，循环结束后，若target在数组中，则left在right的位置
   * 因此返回值left和right都可以
    ~~~python
    def lower_bound(nums: List[int], target: int) -> int:
        l = 0
        r = len(nums)
        while l < r: # 左闭右开区间[l, r)
            m = (l + r) // 2
            if nums[m] < target:
                l = m + 1 # [m + 1, r)
            else:
                r = m # [l, m)
        return l # r
    ~~~
   3. 开区间：(left, right)
    初始值：
      * left = -1, right = n
      * mid = (left + right) // 2
   * 当 left + 1 < right 时，循环执行
   * 若 nums[mid] < target: left = mid
   * 否则 right = mid
   * 这样，循环结束后，若target在数组中，则left在right - 1的位置，当num[mid] == target时，执行 right = mid,而target值索引即为right，故最开始出现的位置为right
   * 因此返回值right
    ~~~python
    def lower_bound(nums: List[int], target: int) -> int:
        l = -1
        r = len(nums)
        while l + 1 < r: # 开区间(l, r)
            m = (l + r) // 2
            if nums[m] < target:
                l = m # (m, r)
            else:
                r = m # (l, m)
        return r
    ~~~
3. 通过这三种思路，我们可以得到target最开始的位置，我们只需要再调用一次函数，将target + 1作为参数，返回值-1即为target最后出现的位置
 
 ### 代码实现
 ~~~python
 class Solution:
    # 调用上述任意一种算法
    def searchRange(self, nums: List[int], target: int) -> List[int]:
        start = lower_bound(nums, target)
        if start == len(nums) or nums[start] != target:
            return [-1,-1]
        end = lower_bound(nums, target + 1) - 1
        return [start,end]
~~~

### 复杂度分析
时间复杂度：**O(log n)** 空间复杂度：**O(1)**
  
