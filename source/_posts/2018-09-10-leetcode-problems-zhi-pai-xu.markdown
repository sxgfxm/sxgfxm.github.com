---
layout: post
title: "LeetCode Problems 之 排序"
date: 2018-09-10 21:53:00 +0800
comments: true
categories: LeetCode
keywords: sxgfxm, 排序
description: sxgfxm, 排序 
---

## 排序
以下为 LeetCode 排序 相关问题解法记录。  
<!-- more -->

### [179. 最大数](https://leetcode-cn.com/problems/largest-number/description/)
问题分析：为组成最大的数，即数字大的排序靠前，问题转化为一种特殊的排序。  
边界情况：当数组中元素全部为0时，转换为字符串后只有一个0。  
代码：  
```swift
class Solution {
    func largestNumber(_ nums: [Int]) -> String {
        var nums = nums
        nums.sort{ String($0) + String($1) > String($1) + String($0) }
        var result = ""
        var flag = false
        for i in nums{
            if i != 0 {
                flag = true
            }
            if flag {
                result = result + String(i)   
            }
        }
        if result.count == 0 {
            result = "0"
        }
        return result
    }
}
```
启发：当数字转换为字符串时，尤其是拼接的情况，需要特殊处理开头连续的0。

### [561. 数组拆分 I](https://leetcode-cn.com/problems/array-partition-i/description/)
问题分析：若a<b<c<d，则min(a,b) + min(c,d) > min(a,c) + min(b,d)，所以按从小到大排序再求和。  
代码：  
```
class Solution {
    func arrayPairSum(_ nums: [Int]) -> Int {
        let array = nums.sorted{$0 < $1}
        var sum = 0
        for i in 0..<array.count / 2 {
            sum += array[i * 2]
        }
        return sum
    }
}
```
积累：`array.sort()`排序自身，`array.sorted()`返回排序后数组。