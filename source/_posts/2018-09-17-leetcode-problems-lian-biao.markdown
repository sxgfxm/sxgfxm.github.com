---
layout: post
title: "LeetCode Problems  链表"
date: 2018-09-17 21:17:45 +0800
comments: true
categories: LeetCode
keywords: sxgfxm，链表
description: sxgfxm，链表
---

## 链表
以下为 LeetCode 链表 相关问题解法记录。  
<!-- more -->

### [88. 合并两个有序数组](https://leetcode-cn.com/problems/merge-sorted-array/description/)
问题分析：链表。  
代码：  
```swift
class Solution {
    func merge(_ nums1: inout [Int], _ m: Int, _ nums2: [Int], _ n: Int) {
        var nums: [Int] = []
        for i in 0..<m {
            nums.append(nums1[i])
        }
        for i in 0..<n {
            nums.append(nums2[i])
        }
        nums1 = nums.sorted{$0 < $1}
        print(nums1)
    }
}
```
启发：需要用 **链表** 实现。  
状态：优于56%的提交。
