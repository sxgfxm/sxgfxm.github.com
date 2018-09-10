---
layout: post
title: "LeetCode Problems 之 数学"
date: 2018-09-10 21:54:51 +0800
comments: true
categories: LeetCode
keywords: sxgfxm, 数学
description: sxgfxm, 数学
---

## 数学
以下为 LeetCode 数学 相关问题解法记录。  
<!-- more -->

### [326. 3的幂](https://leetcode-cn.com/problems/power-of-three/description/)
方法一：模拟3的幂的过程，直到等于或超过给定的值，**时间复杂度为多少？**。  
方法二：通过数学公式转化，3^m = n，log(3^m) = logn，mlog3 = logn，m = logn / log3，判断m是否为整数即可，时间复杂度 O(1)。  
代码：  
```swift
class Solution {
    func isPowerOfThree(_ n: Int) -> Bool {
        if n == 0 {
            return false
        }
        let m = log10(Double(n)) / log10(3.0)
        return m - floor(m) == 0
    }
}
```
启发：通过数学公式简单的变化，将原问题转化为可直接计算的问题，降低复杂度。