---
layout: post
title: "LeetCode Problems 之 哈希"
date: 2018-09-10 21:51:54 +0800
comments: true
categories: LeetCode
keywords: sxgfxm, 哈希
description: sxgfxm, 哈希
---

## 哈希
以下为 LeetCode 哈希 相关问题解法记录。  
<!-- more -->

### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/description/)
问题分析：该问题可转化为给定一个数，查找与另一个数的差是否存在，即查找问题。  
方法一：每次遍历查找，时间复杂度O(n^2)。  
方法二：将数组元素i标记为 `map[num[i]] = i` ，通过下标索引，将查找复杂度由 O(n) 将为 O(1) ，空间复杂度为 O(n) 。  
方法三：边建立哈希表边检查是否存在对应元素，因为是成对的，开始没有找到，之后也会找到，一遍循环搞定。  
代码：  
```swift
class Solution {
    func twoSum(_ nums: [Int], _ target: Int) -> [Int] {

    }
}
```
启发：需要对 **查找** 常用方法进行加深学习。

### [217. 存在重复元素](https://leetcode-cn.com/problems/contains-duplicate/description/)
问题分析：哈希。  
代码：  
```
class Solution {
    func containsDuplicate(_ nums: [Int]) -> Bool {
        var dict: [Int : Int] = [:]
        for i in nums {
            if dict[i] == nil {
                dict[i] = 1
            } else {
                dict[i]! = dict[i]! + 1
            }
            if dict[i]! > 1 {
                return true
            }
        }
        return false
    }
}
```
启发：字典取值需要判断是否为空。  
状态：优于52%的提交。

### [268. 缺失数字](https://leetcode-cn.com/problems/missing-number/description/)
问题分析：哈希表。  
代码：  
```
class Solution {
    func missingNumber(_ nums: [Int]) -> Int {
        var hash: [Int] = []
        for i in 0...nums.count {
            hash.append(0)
        }
        for i in nums {
            hash[i] = 1
        }
        for (i,value) in hash.enumerated() {
            if value == 0 {
                return i
            }
        }
        return 0
    }
}
```
思考：如何用常数额外空间O(n)时间复杂度解决？