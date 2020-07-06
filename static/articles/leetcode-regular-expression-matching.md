# Leetcode - Regular Expression Matching
leetcode의 [Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching) 문제이다.
난이도: hard

## Problem
주어진 문자열과 패턴 문자열(정규표현식)을 비교하여 매치되는지 여부를 반환하는 문제이다.

```sh
'.' Matches any single character.
'*' Matches zero or more of the preceding element.
```

## How to solve
*O(n)* 인 방법이 있을까 한참을 고민하다가 결국 *O(n^2)* DP로 풀긴하였다. 만약 이 문제가 40분제한이있었다면.. 놓쳤을 듯하다. 시간제한이 있다면 너무 욕심부리지 말고 우선 가능한 솔루션을 내놓고나서 더 빠른 방법을 고민하는 버릇을 들여야겠다.

### Dynamic Promgramming
```sh
dp[i][j] : 0~i의 문자열이 0~j까지의 패턴과 매치되는지 여부
```
위와 같이 정의한다면 매치가 가능한 경우는 아래와 같이 세가지 경우가 있다.

1. i-1까지의 문자열과 j-1까지의 패턴이 이미 매칭되어 있다면 i번째 문자와 j번째 패턴만 일치하면 된다
    문자열 `abc[d]`, 패턴 `abc[d]`
1. i-1까지의 문자열과 j까지의 패턴이 이미 매칭되어 있다면, i번째 문자는 j번째 패턴과 일치하면서 j번째 패턴은 *로 끝나야한다.
    문자열 `aad[d]`, 패턴 `a*d*`
1. i까지의 문자열과 j-1까지의 패턴이 이미 매칭되어 있다면, j번째 패턴은 *로 끝나야한다.
    문자열 `aadd`, 패턴 `aad*[c*]`

이 내용을 조금 더 코딩스럽게 바꾸면 아래처럼 변환된다.

1. dp[i-1][j-1]이 True 이고 s[i] 와 p[j]가 매칭되면, True
1. dp[i-1][j]가 True 이고 s[i] 와 p[j]가 매칭되면서 p[j]가 *로 끝나면, True
1. dp[i][j-1]이 True 이고 p[j]가 *로 끝나면, True

## Solution

```py
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        jArr = []
        for i in range(len(p)):
            if p[i] == '*':
                jArr.append(jArr.pop() + '*')
            else:
                jArr.append(p[i])
        I = len(s) + 1
        J = len(jArr) + 1
        dp = [[False] * J for x in range(I)]
        dp[0][0] = True
        for j in range(1, J):
            if jArr[j-1].endswith('*'):
                dp[0][j] = True
            else:
                break
        for i in range(1, I):
            for j in range(1, J):
                pattern = jArr[j-1]
                char = s[i-1]
                if dp[i-1][j-1] and self.isCharMatch(pattern, char):
                    dp[i][j] = True
                elif dp[i-1][j] and pattern.endswith('*') and self.isCharMatch(pattern, char):
                    dp[i][j] = True
                elif dp[i][j-1] and pattern.endswith('*'):
                    dp[i][j] = True
        return dp[I - 1][J - 1]

    def isCharMatch(self, pattern: str, char: str):
        return pattern.startswith(char) or pattern.startswith('.')
```

## Result
Success
Runtime: 48 ms (상위 4% 쯤)
Memory Usage: 13.2 MB