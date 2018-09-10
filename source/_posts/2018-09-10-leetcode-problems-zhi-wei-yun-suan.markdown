---
layout: post
title: "LeetCode Problems 之 位运算"
date: 2018-09-10 21:53:58 +0800
comments: true
categories: LeetCode
keywords: sxgfxm, 位运算
description: sxgfxm, 位运算
---

## 位运算
以下为 LeetCode 位运算 相关问题解法记录。  
<!-- more -->

### [231. 2的幂](https://leetcode-cn.com/problems/power-of-two/description/)
方法一、二：同题目[326. 3的幂](https://leetcode-cn.com/problems/power-of-three/description/)。  
方法三：考察2的幂的特性，转化为二进制形式为1后面n个0，可以通过位运算来判断是否符合该形式。  
代码：  
```swift
class Solution {
    func isPowerOfTwo(_ n: Int) -> Bool {
      if n == 0 {
            return false
        }
        return (n - 1) & n == 0
    }
}
```
启发：有时候可以考察数字的二进制、八进制、十六进制形式，从中寻找规律。

### [342. 4的幂](https://leetcode-cn.com/problems/power-of-four/description/)
方法一、二、三：同题目[231. 2的幂](https://leetcode-cn.com/problems/power-of-two/description/)，位运算方式稍微改变。  
代码：  
```
class Solution {
    func isPowerOfFour(_ n: Int) -> Bool {
    }
}
```
积累：负数的位运算操作。格式化输出。