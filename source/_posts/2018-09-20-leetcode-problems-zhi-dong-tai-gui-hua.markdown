---
layout: post
title: "LeetCode Problems 之 动态规划"
date: 2018-09-20 12:53:25 +0800
comments: true
categories: LeetCode
keywords: sxgfxm，动态规划
description: sxgfxm，动态规划
---

## 动态规划
以下为 LeetCode 动态规划 相关问题解法记录。  
<!-- more -->

### [70. 爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/description/)
问题分析：动态规划，f[i]表示第i阶的方法数，f[i] = f[i - 1] + f[i - 2]，f[0] = 1。  
代码：  
```swift
class Solution {
    func climbStairs(_ n: Int) -> Int {
        var f: [Int] = []
        f.append(1)
        for i in 1...n {
            var one = f[i - 1]
            var two = 0
            if (i - 2 < 0) {
                two = 0
            } else {
                two = f[i - 2]
            }
            f.append(one + two)
        }
        return f[n]
    }
}
```
启发：递推。

### [746. 使用最小花费爬楼梯](https://leetcode-cn.com/problems/min-cost-climbing-stairs/description/)
问题分析：动态规划，f[i]表示第i阶最小花费，f[i] = min(f[i - 1] + cost[i], f[i - 2] + cost[i])，f[0] = cost[0]。  
代码：  
```swift
class Solution {
    func minCostClimbingStairs(_ cost: [Int]) -> Int {
        var f: [Int] = []
        f.append(cost[0])
        for i in 1...(cost.count + 1) {
            var one = f[i - 1]
            var two = 0
            var now = 0
            if i - 2 < 0 {
                two = 0
            } else {
                two = f[i - 2]
            }
            if i >= cost.count {
                now = 0
            } else {
                now = cost[i]
            }
            f.append(min(one + now, two + now))
        }
        return min(f[cost.count], f[cost.count + 1])
    }
}
```
启发：可以直接使用min()，max()函数。
