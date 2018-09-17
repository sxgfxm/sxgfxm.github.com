---
layout: post
title: "LeetCode Problems 之 深度优先搜索"
date: 2018-09-10 21:55:31 +0800
comments: true
categories: LeetCode
keywords: sxgfxm, 深度优先搜索
description: sxgfxm, 深度优先搜索
---

## 深度优先搜索
以下为 LeetCode 深度优先搜索 相关问题解法记录。  
<!-- more -->

### [717. 1比特与2比特字符](https://leetcode-cn.com/problems/1-bit-and-2-bit-characters/description/)
问题分析：深度优先搜索，按状态转移搜索。  
代码：  
```swift
class Solution {
    func isOneBitCharacter(_ bits: [Int]) -> Bool {
        return dfs(bits, 0)
    }

    func dfs(_ bits:[Int], _ index: Int) -> Bool {
        if index == bits.count - 1 && bits[index] == 0 {
            return true
        }
        if index >= bits.count {
            return false
        }
        if index < bits.count {
            if bits[index] == 0{
                return dfs(bits, index + 1)
            }
            if index + 1 < bits.count {
                if bits[index] == 1 && bits[index + 1] == 0 {
                    return dfs(bits, index + 2)
                }
                if bits[index] == 1 && bits[index + 1] == 1 {
                    return dfs(bits, index + 2)
                }
            }
        }
        return false   
    }
}
```
启发：按状态转移搜索。  
状态：优于100%的提交。  

### [695. 岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island/description/)
问题分析：非回溯4个方向求和。  
代码：  
```swift
class Solution {
    var maxCount = 0

    let transform = [[0,1],[0,-1],[1,0],[-1,0]]

    func maxAreaOfIsland(_ grid: [[Int]]) -> Int {
        var grid = grid
        for i in 0..<grid.count {
            for j in 0..<grid[0].count {
                if grid[i][j] == 1 {
                    let count = dfs(&grid, i, j)
                    if maxCount < count {
                        maxCount = count
                    }
                }
            }
        }
        return maxCount
    }

    func dfs(_ grid: inout [[Int]], _ x: Int, _ y: Int) -> Int {
        if x >= 0 && x < grid.count && y >= 0 && y < grid[0].count && grid[x][y] == 1 {
            grid[x][y] = 0
            var count = 1
            for i in 0..<4 {
                let xx = x + transform[i][0]
                let yy = y + transform[i][1]
                count = count + dfs(&grid, xx, yy)
            }   
            return count
        } else {
            return 0
        }
    }
}
```

### [200. 岛屿的个数](https://leetcode-cn.com/problems/number-of-islands/description/)
问题分析：深度优先遍历，遇到0置为1，非回溯。  
代码：  
```
class Solution {
    var count = 0
    let transform = [[0,1],[0,-1],[1,0],[-1,0]]

    func numIslands(_ grid: [[Character]]) -> Int {
        var grid = grid
        for i in 0..<grid.count {
            for j in 0..<grid[0].count {
                if grid[i][j] == "1" {
                    dfs(&grid, i, j)
                    count = count + 1
                }
            }
        }
        return count
    }

    func dfs(_ grid: inout [[Character]], _ x: Int, _ y: Int) -> Void {
        grid[x][y] = "0"
        for i in 0..<4 {
            let xx = x + transform[i][0]
            let yy = y + transform[i][1]
            if xx >= 0 && xx < grid.count && yy >= 0 && yy < grid[0].count && grid[xx][yy] == "1" {
                dfs(&grid, xx, yy)   
            }
        }
    }
}
```
