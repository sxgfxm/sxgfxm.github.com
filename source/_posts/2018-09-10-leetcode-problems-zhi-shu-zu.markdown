---
layout: post
title: "LeetCode Problems 之 数组"
date: 2018-09-10 21:49:03 +0800
comments: true
categories: LeetCode
keywords: sxgfxm, 数组
description: sxgfxm, 数组
---

## 数组
以下为 LeetCode 数组 相关问题解法记录。  
<!-- more -->

### [66. 加一](https://leetcode-cn.com/problems/plus-one/description/)
问题分析：高精度加1，先加1，再处理进位。  
代码：  
```swift
class Solution {
    func plusOne(_ digits: [Int]) -> [Int] {
        var digits = digits
        let length = digits.count - 1
        var value = digits[length] + 1
        digits[length] = (value) % 10
        var c = (value) / 10
        for i in (0..<length).reversed() {
            value = digits[i] + c
            digits[i] = (value) % 10
            c = value / 10
        }
        if c > 0 {
            digits.insert(c, at:0)
        }
        return digits
    }
}
```  
启发：array.insert(value, at:index)，(0..100).reversed()。  
状态：优于55%的提交。  

### [674. 最长连续递增序列](https://leetcode-cn.com/problems/longest-continuous-increasing-subsequence/description/)
问题分析：模拟。  
代码：  
```swift
class Solution {
    func findLengthOfLCIS(_ nums: [Int]) -> Int {
        if nums.count == 0 {
            return 0
        }
        if nums.count == 1 {
            return 1
        }
        var maxLength = 1
        var count = 1
        for i in 1..<nums.count {
            if nums[i] > nums[i - 1] {
                count = count + 1
            } else {
                count = 1
            }
            maxLength = max(maxLength, count)
        }
        return maxLength
    }
}
```  
启发：注意边界情况。  
状态：优于100%的提交。

### [747. 至少是其他数字两倍的最大数](https://leetcode-cn.com/problems/largest-number-at-least-twice-of-others/description/)
问题分析：模拟。  
代码：  
```swift
class Solution {
    func dominantIndex(_ nums: [Int]) -> Int {
        var maxValue = -1
        var maxIndex = -1
        for (index, value) in nums.enumerated() {
            if value > maxValue {
                maxValue = value
                maxIndex = index
            }
        }
        for (index, value) in nums.enumerated() {
            if index != maxIndex {
                if value * 2 > maxValue {
                    return -1
                }
            }
        }
        return maxIndex
    }
}
```

### [888. 公平的糖果交换](https://leetcode-cn.com/problems/fair-candy-swap/description/)
问题分析：给定一个数，找另一个数。  
方法一：模拟，双重循环，sumA + (B[j] - A[i]) == sumB + (A[i] - B[j])，时间复杂度O(n^2)。  
代码：  
```swift
class Solution {
    func fairCandySwap(_ A: [Int], _ B: [Int]) -> [Int] {
        var sumA = 0
        var sumB = 0
        A.map{sumA = sumA + $0}
        B.map{sumB = sumB + $0}
        for i in A {
            for j in B {
                if (sumA + j - i) == (sumB + i - j) {
                    return [i, j]
                }
            }
        }
        return []
    }
}
```  
方法二：哈希，B[j] = (sumB + 2 * A[i] - sumA) / 2，时间复杂度O(n)，空间复杂度O(n)。  
```swift
class Solution {
    func fairCandySwap(_ A: [Int], _ B: [Int]) -> [Int] {
        var sumA = 0
        var sumB = 0
        var hash: [Int:Bool] = [:]
        A.map{sumA = sumA + $0}
        B.map{sumB = sumB + $0}
        for i in A {
            let key = (sumB + 2 * i - sumA) / 2
            if let value = hash[key], value == true {
                return [i, key]
            }
        }
        return []
    }
}
```  
方法三：排序 + 二分查找，不申请额外空间。  
```swift
class Solution {
    func fairCandySwap(_ A: [Int], _ B: [Int]) -> [Int] {
        var B = B
        var sumA = 0
        var sumB = 0
        A.map{sumA = sumA + $0}
        B.map{sumB = sumB + $0}
        B.sort{$0 < $1}
        for i in A {
            let target = (sumB + 2 * i - sumA) / 2
            if binarySearch(B, 0, B.count - 1, target) {
                return [i, target]
            }
        }
        return []
    }

    func binarySearch(_ nums: [Int], _ left: Int, _ right: Int, _ target: Int) -> Bool {
        if left <= right {
            if nums[left] == target {
                return true
            }
            if nums[right] == target {
                return true
            }
            let mid = (left + right) / 2
            if nums[mid] > target {
                binarySearch(nums, left + 1, mid, target)
            } else {
                binarySearch(nums, mid, right - 1, target)
            }
        }
        return false
    }
}
```
启发：给定一个数，查找另一个数。

### [830. 较大分组的位置](https://leetcode-cn.com/problems/positions-of-large-groups/description/)
问题分析：模拟，注意考虑边界情况。  
代码：  
```swift
class Solution {
    func largeGroupPositions(_ S: String) -> [[Int]] {
        var result: [[Int]] = []
        var left = 0, right = 0, count = 0
        while left < S.count {
            right = left + 1
            count = 1
            while right < S.count {
                if S[S.index(S.startIndex, offsetBy: right)] == S[S.index(S.startIndex, offsetBy: left)] {
                    count = count + 1
                    right = right + 1
                } else {
                    if count >= 3 {
                        result.append([left, right - 1])
                    }
                    break
                }
                if count >= 3 && right == S.count - 1{
                    result.append([left, right])
                }
            }
            left = right

        }
        return result
    }
}
```
启发：注意边界条件。  
状态：优于25%的提交。

### [904. 水果成篮](https://leetcode-cn.com/contest/weekly-contest-102/problems/fruit-into-baskets/)
问题分析：最长连续子序列长度，且子序列不同个数最多为2。  
代码：  
```swift
class Solution {
    func totalFruit(_ tree: [Int]) -> Int {
        var set = Set<Int>()
        for i in tree {
            set.insert(i)
        }
        if set.count <= 2 {
            return tree.count
        }
        var maxLength = 0
        for i in 0..<tree.count {
            var set = Set<Int>()
            set.insert(tree[i])
            var count = 1
            for j in (i+1)..<tree.count {
                if set.count == 1 {
                    set.insert(tree[j])
                    count = count + 1
                } else if set.contains(tree[j]){
                    count = count + 1
                } else {
                    break
                }
            }
            if maxLength < count {
                maxLength = count
            }
        }
        return maxLength
    }
}
```
启发：**不是最佳解法**。

### [905. 按奇偶校验排序数组](https://leetcode-cn.com/contest/weekly-contest-102/problems/sort-array-by-parity/)
问题分析：模拟。  
代码：  
```swift
class Solution {
    func sortArrayByParity(_ A: [Int]) -> [Int] {
        var nums: [Int] = []
        for i in A {
            if i % 2 == 0 {
                nums.append(i)
            }
        }
        for i in A {
            if i % 2 != 0 {
                nums.append(i)
            }
        }
        return nums
    }
}
```

### [35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/description/)
问题分析：二分查找。  
方法一：顺序查找，时间复杂度O(N)。  
代码：  
```swift
class Solution {
    func searchInsert(_ nums: [Int], _ target: Int) -> Int {
        for (index, value) in nums.enumerated() {
            if value >= target {
                return index
            }
        }   
        return nums.count
    }
}
```
方法二：二分查找，时间复杂度O(logN)。  
代码：  
```

```
启发：二分查找。  
状态：；优于40%提交。

### [697. 数组的度](https://leetcode-cn.com/problems/degree-of-an-array/description/)
问题分析：//  问题分析：先找出数组的度，然后前后查找对应的值的位置。  
代码：  
```swift
class Solution {
    func findShortestSubArray(_ nums: [Int]) -> Int {
        var hash: [Int:Int] = [:]
        var maxCount = 0
        for i in nums {
            if hash[i] == nil {
                hash[i] = 1
            } else {
                hash[i]! = hash[i]! + 1
            }
            if maxCount < hash[i]! {
                maxCount = hash[i]!
            }
        }
        if maxCount == 1 {
            return 1
        }
        var dus:[Int] = []
        for (key, value) in hash{
            if value == maxCount {
                dus.append(key)
            }
        }
        var minLength = 50000
        for maxValue in dus {
            var left = 0
            var right = nums.count - 1
            for (index, value) in nums.enumerated() {
                if value == maxValue {
                    left = index
                    break
                }
            }
            for (index, value) in nums.enumerated().reversed() {
                if value == maxValue {
                    right = index
                    break
                }
            }
            if minLength > right - left + 1 {
                minLength = right - left + 1
            }
        }
        return minLength

    }
}
```
启发：审清题目。  
状态：优于80%的提交。

### [167. 两数之和 II - 输入有序数组](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/description/)
问题分析：因为已按升序排序，只需前后两个指针移动即可。  
```swift
class Solution {
    func twoSum(_ numbers: [Int], _ target: Int) -> [Int] {
        var i = 0, j = numbers.count - 1
        while i < j {
            let value = numbers[i] + numbers[j]
            if value > target {
                j -= 1
            } else if value < target {
                i += 1
            } else {
                return [i + 1, j + 1]
            }
        }
        return []
    }
}
```
启发：依据已有条件找出效率更高的解法，而非直接双重循环。  
状态：优于66%的提交。

### [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/description/)
问题分析：枚举所有情况求值。  
代码：  
```
class Solution {
    func maxProfit(_ prices: [Int]) -> Int {
        var maxProfit = 0
        for i in 0..<prices.count {
            for j in (i+1)..<prices.count {
                let profit = prices[j] - prices[i]
                if maxProfit < profit {
                    maxProfit = profit
                }
            }
        }
        return maxProfit
    }
}
```

### [661. 图片平滑器](https://leetcode-cn.com/problems/image-smoother/description/)
问题分析：图片滤波器，构造转换方式。  
代码：  
```
class Solution {

    let transform = [[-1, -1], [0, -1], [1, -1],
                     [-1, 0], [0, 0], [1, 0],
                     [-1, 1], [0, 1], [1, 1]]

    func imageSmoother(_ M: [[Int]]) -> [[Int]] {
        var N: [[Int]] = []
        let row = M.count
        let colum = M[0].count
        for i in 0..<row {
            var array: [Int] = []
            for j in 0..<colum {
                var value = 0
                var count = 0
                for k in 0..<9 {
                    let xx = i + transform[k][0]
                    let yy = j + transform[k][1]
                    if xx >= 0 && xx < row && yy >= 0 && yy < colum {
                        value = value + M[xx][yy]
                        count = count + 1
                    }
                }
                array.append(Int(value / count))
            }
            N.append(array)
        }
        return N
    }
}
```
思考：有没有更简洁的写法？  
状态：优于75%的提交。

### [867. 转置矩阵](https://leetcode-cn.com/problems/transpose-matrix/description/)
问题分析：模拟矩阵转置过程，AT[i][j] = A[j][i]，i ~ [0,m]，j ~ [0,n]。  
代码：  
```
class Solution {
    func transpose(_ A: [[Int]]) -> [[Int]] {
        var AT: [[Int]] = []
        for i in 0..<A[0].count {
            var row: [Int] = []
            for j in 0..<A.count {
                row.append(A[j][i])
            }
            AT.append(row)
        }
        return AT
    }
}
```

### [118. 杨辉三角](https://leetcode-cn.com/problems/pascals-triangle/description/)
问题分析：a[i,j] = a[i-1,j-1] + a[i-1,j]，i ~ [0,numRows)，j ~ [0,i]，超出边界为0。  
代码：  
```
class Solution {
    func generate(_ numRows: Int) -> [[Int]] {
        var rows: [[Int]] = []
        for i in 0..<numRows {
            var row: [Int] = []
            for j in 0...i {
                var left = 0
                var right = 0
                if i - 1 < 0 {
                    left = 1
                } else {
                    if j - 1 < 0 {
                        left = 0
                    } else {
                        left = rows[i-1][j-1]
                    }
                    if j >= rows[i-1].count {
                        right = 0
                    } else {
                        right = rows[i-1][j]
                    }
                }
                row.append(left + right)
            }
            rows.append(row)
        }
        return rows
    }
}
```
**还有更简洁的写法吗？**

### [119. 杨辉三角 II](https://leetcode-cn.com/problems/pascals-triangle-ii/description/)
问题分析：模拟，压缩空间，倒序生成。  
代码：  
```
class Solution {
    func getRow(_ rowIndex: Int) -> [Int] {
        if rowIndex == 0 {
            return [1]
        }
        var nums: [Int] = []
        for i in 0...rowIndex {
            nums.append(0)
        }
        nums[0] = 1
        for i in 1...rowIndex {
            for j in stride(from: i, to: 0, by: -1) {
                nums[j] = nums[j] + nums[j - 1]
            }
        }
        return nums
    }
}
```
积累：倒序遍历数组。

### [566. 重塑矩阵](https://leetcode-cn.com/problems/reshape-the-matrix/description/)
问题分析：reshapeNums[i,j] = nums[index / colums, index % colums], index++。  
边界情况：nums.count * nums[0].count = r * c，nums为空的情况。  
代码：  
```
class Solution {
    func matrixReshape(_ nums: [[Int]], _ r: Int, _ c: Int) -> [[Int]] {
        let row: Int = nums.count
        if row == 0 {
            return nums
        }
        let colum: Int = nums[0].count
        if colum == 0 {
            return nums
        }
        //  不可以转换
        if row * colum != r * c {
            return nums
        }
        //  转换
        var index: Int = 0;
        var reshapeNums: [[Int]] = []
        for _ in 0..<r {
            var rowArray: [Int] = []
            for _ in 0..<c {
                rowArray.append(nums[index / colum][index % colum])
                index = index + 1
            }
            reshapeNums.append(rowArray)
        }
        return reshapeNums
    }
}
```
积累：未使用循环变量可以用`_`代替。

### [766. 托普利茨矩阵](https://leetcode-cn.com/problems/toeplitz-matrix/description/)
问题分析：保持对角线相同，观察特征，每行右移一位与下一行除第一位开始完全相同，按此方法检测，且空间复杂度为O(n)。  
代码：  
```
class Solution {
    func isToeplitzMatrix(_ matrix: [[Int]]) -> Bool {
        //  初始化
        var testRow = matrix[0]
        if matrix.count == 1 {
            return true
        }
        //  依次检测每行
        for i in 1..<matrix.count {
            testRow.remove(at:testRow.count - 1)
            testRow.insert(matrix[i][0], at:0)
            for j in 0..<matrix[i].count {
                if testRow[j] != matrix[i][j] {
                    return false
                }
            }
        }
        return true
    }
}
```
积累：`array.insert(value, at:index)`插入数组元素，`array.remove(at:index)`删除数组元素。

### [169. 求众数](https://leetcode-cn.com/problems/majority-element/description/)
问题分析：统计元素出现个数，并找出出现个数大于n/2的元素。  
方法一：从小到大排序，然后统计个数。  
方法二：由于众数个数大于n/2，所以中间位置必为众数，直接返回即可。  
代码：  
```
class Solution {
    func majorityElement(_ nums: [Int]) -> Int {
        //  排序
        let sortedNums = nums.sorted{$0 < $1}
        //  取中位数
        return sortedNums[nums.count / 2]
    }
}
```

### [283. 移动零](https://leetcode-cn.com/problems/move-zeroes/description/)
问题分析：模拟移动零的过程，如果第i位为0，则用第i位后第一个非0值交换。  
代码：  
```
class Solution {
    func moveZeroes(_ nums: inout [Int]) {
        for i in 0..<nums.count {
            if nums[i] == 0 {
                var index = i
                for j in (i+1)..<nums.count {
                    if nums[j] != 0 {
                        index = j
                        break
                    }
                }
                if index != i {
                    var temp = nums[i]
                    nums[i] = nums[index]
                    nums[index] = temp
                }
            }
        }
    }
}
```

### [485. 最大连续1的个数](https://leetcode-cn.com/problems/max-consecutive-ones/description/)
问题分析：统计连续1的个数并取最大值
代码：  
```
class Solution {
    func findMaxConsecutiveOnes(_ nums: [Int]) -> Int {
        var maxCount = 0
        var currentCount = 0
        for i in nums {
            if i == 0 {
                currentCount = 0
            } else {
                currentCount += 1
            }
            if maxCount < currentCount {
                maxCount = currentCount
            }
        }
        return maxCount
    }
}
```

### [896. 单调数列](https://leetcode-cn.com/problems/monotonic-array/description/)
问题分析：同时记录是否为单调递增或者单调递减。  
代码：  
```
class Solution {
    func isMonotonic(_ A: [Int]) -> Bool {
        var isIncrease = true
        var isDecrease = true
        for i in 1..<A.count {
            if A[i-1] > A[i] {
                isIncrease = false
                break
            }
        }
        for i in 1..<A.count {
            if A[i-1] < A[i] {
                isDecrease = false
                break
            }
        }
        return isIncrease || isDecrease

    }
}
```
