# Leetcode - String to Integer (atoi)
leetcode의 [String to Integer (atoi)](https://leetcode.com/problems/string-to-integer-atoi/) 문제이다.
난이도: medium

## Problem
주어진 문자열을 숫자로 변환해야하는데 몇 가지 조건이 있다.
1. 전체 문자열 앞에는 공백이 있을 수 있다.
1. 공백이 아닌 첫 문자 ~ 숫자가 아닌 다른 문자가 나오기 직전까지의 문자열을 숫자로 바꾸어야한다.
1. 숫자가 될 문자열은 부호(+ 혹은 -)가 있을 수 있다.
1. 숫자로 바꿀 수 없는 경우는 0을 반환해야 한다.
1. 32-bit 숫자 범위를 넘는 경우 MIN_VALUE(-2147483648) 혹은 MAX_VALUE(2147483647)을 반환해야 한다.

```sh
Input: "42"
Output: 42
```

```sh
Input: "   -42"
Output: -42
Explanation: The first non-whitespace character is '-', which is the minus sign.
             Then take as many numerical digits as possible, which gets 42.
```

```sh
Input: "4193 with words"
Output: 4193
Explanation: Conversion stops at digit '3' as the next character is not a numerical digit.
```

```sh
Input: "words and 987"
Output: 0
Explanation: The first non-whitespace character is 'w', which is not a numerical 
             digit or a +/- sign. Therefore no valid conversion could be performed.
```

```sh
Input: "-91283472332"
Output: -2147483648
Explanation: The number "-91283472332" is out of the range of a 32-bit signed integer.
             Thefore INT_MIN (−231) is returned.
```


## How to solve
정규표현식과 Clamp 트릭으로 간단히 풀었다. 아마 문제에서 의도 하고자 하는 바는 각각의 케이스를 고려해서 반복문을 돌며 짜길 바란거 같은데... 

실무에서 이런 요구사항이 생겼다면, 아래처럼 간단한 코드를 쓰지 않을까 싶다.. 정규표현식 만세!

## Solution

```py
from re import compile
class Solution:
    def myAtoi(self, s: str) -> int:
        MIN_VALUE, MAX_VALUE = - 2 ** 31, 2 ** 31 - 1
        pattern = compile(r'^ *([\+-]?\d+){1}')
        matched = pattern.match(s)
        if matched:
            return min(MAX_VALUE, max(MIN_VALUE, int(matched.group())))
        else:
            return 0

```

## Result
Success
Runtime: 44 ms (상위 15% 쯤)
Memory Usage: 13.3 MB