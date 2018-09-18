---
layout: post
title: "LeetCode Problems之 分治法"
date: 2018-09-18 21:11:05 +0800
comments: true
categories: LeetCode
keywords: sxgfxm，分治法
description: sxgfxm，分治法
---

## 分治法
以下为 LeetCode 分治法 相关问题解法记录。  
<!-- more -->

### [53. 最大子序和](https://leetcode-cn.com/problems/maximum-subarray/description/)
问题分析：求连续子序列最大和。  
方法一：模拟。  
代码：  
```swift
class Solution {
    func maxSubArray(_ nums: [Int]) -> Int {
        var maxValue = Int.min
        for i in 0..<nums.count {
            var sum = nums[i]
            if maxValue < sum {
                maxValue = sum
            }
            for j in (i+1)..<nums.count {
                sum = sum + nums[j]
                if maxValue < sum {
                    maxValue = sum
                }
            }
        }
        return maxValue
    }
}
```
方法二：动态规划。  
代码：  
```
//  待完成
```  
方法三：分治法。  
```
//  待完成
```  
启发：**连续子序列问题**，分治法。
