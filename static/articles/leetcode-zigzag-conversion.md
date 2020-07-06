# Leetcode - ZigZag Conversion
leetcode의 [ZigZag Conversion](https://leetcode.com/problems/zigzag-conversion) 문제이다.
난이도: medium

## Problem
주어진 문자열을 지그재그로 재배열한 새 문자열을 반환하는 문제이다. 지그재그의 길이까지 고려해야 한다.

```sh
Input: s = "PAYPALISHIRING", numRows = 3
Output: "PAHNAPLSIIGYIR"

P   A   H   N
A P L S I I G
Y   I   R

```
```sh
Input: s = "PAYPALISHIRING", numRows = 4
Output: "PINALSIGYAHRPI"
Explanation:

P     I    N
A   L S  I G
Y A   H R
P     I
```

## How to solve
생각보다 쉽게 풀린 문제였다. 방법이야 많겠지만, 시간복잡도가 *O(n)*을 만족하면 통과하는 것 같다.
나머지 값을 계산하여 배열에 나누어 넣고 합치는 걸로 완성

## Solution

```py
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1:
            return s
        factor = 2 * numRows - 2
        arr = [[] for x in range(numRows)]
        for i in range(len(s)):
            nam = i % factor
            gap = min(nam, factor - nam)
            arr[gap].append(s[i])
        return ''.join(''.join(arr[i]) for i in range(numRows))

```

## Result
Success
Runtime: 80 ms (상위 40% 쯤)
Memory Usage: 13.1 MB