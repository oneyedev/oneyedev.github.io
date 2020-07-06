# Leetcode - Integer to Roman
leetcode의 [Integer to Roman](https://leetcode.com/problems/integer-to-roman) 문제이다.
난이도: medium

## How to solve
시간복잡도의 문제라기 보다는 쉬워 보이지만 생각보다 헷갈리는 로직을 얼마나 빨리 정확하게 짤 수 있는지 보는 문제로 보인다(그리고 아마도 가독성까지).

각 숫자의 자리수 에 따른 한자리 숫자를 가져와 로마자 규칙에 따른 변환 메소드로 바꾼 후 합쳤다.

## Solution
```py
class Solution:
    def intToRoman(self, num: int) -> str:
        a = num // 1000 % 10
        b = num // 100 % 10
        c = num // 10 % 10
        d = num // 1 % 10
        strA = self.intToRomanChar(a, 'M', '', '')
        strB = self.intToRomanChar(b, 'C', 'D', 'M')
        strC = self.intToRomanChar(c, 'X', 'L', 'C')
        strD = self.intToRomanChar(d, 'I', 'V', 'X')
        return f'{strA}{strB}{strC}{strD}'

    def intToRomanChar(self, num: int, one, five, ten) -> str:
        if num == 4:
            return one + five
        if num == 9:
            return one + ten
        base = five if num > 4 else ''
        return base + ''.join([one for _ in range(num % 5)])

```

## Result
Success
Runtime: 52 ms (상위 8% 쯤)
Memory Usage: 13.3 MB