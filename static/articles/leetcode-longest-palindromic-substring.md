# Leetcode - Longest Palindromic Substring
leetcode의 [Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring) 문제이다.
난이도: medium

## Problem
주어진 문자열의 부분 문자열 중 가장 긴 회문을 찾는 문제이다. 회문이란 가운데를 기준으로 맞은 편의 문자가 동일한 구문, 한글로 치자면 "토마토", "요요", 같은 구문을 말한다. 줄여서 LPS(Longest Palindromic Substring)라고 하자.

```sh
Input: "babad"
Output: "bab"
Note: "aba" is also a valid answer.
```

```sh
Input: "cbbd"
Output: "bb"
```

## How to solve
개인적으로 공부가 많이 된 문제였다. 기본 접근법은 DP이지만, 조금 더 생각해서 최적화된 접근을 할 수 있었다.

### Dynamic Programming
```sh
isPalibdromic(i, j): i ~ j 부분 문자열이 회문인지 여부
str(i): i번째 문자
```

라고 정의 하면

```sh
isPalibdromic(i, j) = isPalibdromic(i+1, j-1) && str(i) == str(j)
```

가 성립한다. 즉,

```sh
asdsa : 가 회문이기 위해서는 안쪽의
 sds  : 가 회문이면서 왼쪽의 문자(a)와 오른쪽의문자(a)가 같아야 한다는 뜻이다.
```

이 특성을 이용하면 LPS를 찾을 수 있다. 프로그래밍에 친숙하게 2차원 배열로 접근해보자. `banana`라는 문자열이 있을 때 회문 여부는 아래와 같다.

```sh
  b a n a n a
b O X X X X X
a   O X O X O <-- LPS (anana)
n     O X O X
a       O X O
n         O X
a           O 
```
기저사례 `i = j` 대각선과 `i = j - 1` 대각선을 미리 채워놓고, 나머지 부분을 채워나가면된다. 회문 판별 여부는 위의 규칙에 따라 대각선 왼쪽아래(`i+1`, `j-1`)가 `True`이면서 i와 j위치의 문자가 같으면 `True`이다. 가장 오른쪽 상단에 있는 `True`가 LPS가 된다. 시간복잡도는 *O(n^2)*

해당 로직을 구현 후 제출하니 통과하는 하지만 시간이 3~4초 쯤으로 못내 아쉬웠다(하위 20% 쯤). 그래서 어떻게 하면 더 빠르게 할 수 있을까 고민해보았다. 

## Expand Around Center + Pruning
보다 쉬운 계산을 위해서 약간의 트릭을 추가하였다.

```sh
        b   a   n   a   n   a
index:  0 1 2 3 4 5 6 7 8 9 10
```

위 처럼 인덱스를 지정하면 회문 판별을 편하게 할 수 있다. 예를 들어 인덱스가 `0`이면 첫 `b`에서, `1`이면 `ba`에서 시작하여 회문 판별을 넓혀나가는 것이다. 

여기서도 단순히 `linear`(인덱스 반복) x `linear`(회문 판별)로 실행하면 여전히 시간복잡도는 *O(n^2)*일 것이므로 가지치기를 고민해보았다. 

인덱스 `i`에서 확장가능한 최대 길이는 왼편 길이와 오른편 길이 중 작은 값이므로 아래와 같이 계산할 수 있다.

```sh
maxLength(i) =  min(i, length - i)
```

그러므로 현재까지 발견된 LPS의 길이가 `maxLength(i)`보다 길면 인덱스 `i`는 검색할 필요가 없으며, 만일 탐색 순서가 `maxLength(i)` 내림차순으로 이루어진다면 반복문 자체를 아예 종료시킬수 있을 것이다!

`maxLength(i)`는 가운데 인덱스가 가장 길고 멀어질수록 줄으므로, 가운데에서 시작해 BFS를 이용하여 로직을 구현하였다.

> 가운데 인덱스를 제외하고 모두 자식이 1개 밖에 없어 `queue`를 굳이 사용하지 않아도 되는 건 함정...

## Solution
```py
import queue

class Solution:
    def longestPalindrome(self, s: str) -> str:
        length = len(s) * 2 - 1
        if length < 0:
            return ''
        center = len(s) - 1
        lpsStart = 0
        lpsEnd = 0
        q = queue.Queue()
        # Start at center index
        q.put(center)
        while not q.empty():
            # Dequeue a index
            i = q.get()

            # Check a maximum LPS length
            lpsLength = lpsEnd - lpsStart
            maxLength = min(i, length - i)
            if maxLength < lpsLength:
                break
            
            # Expand Palindromic
            u = i // 2
            v = i - u
            while u >= 0 and v < len(s):
                if s[u] != s[v]:
                    break
                if v - u > lpsLength:
                    lpsStart = u
                    lpsEnd = v
                u -= 1
                v += 1

            # Enqueue Children
            for child in self.getChildren(center, i):
                q.put(child)
        return s[lpsStart: lpsEnd + 1]

    def getChildren(self, center: int, i: int):
        if i > center:
            return [i + 1]
        elif i < center:
            return [i - 1]
        else:
            return [i - 1, i + 1]
```

## Result
Success
Runtime: 416 ms (상위 12% 쯤)
Memory Usage: 13.6 MB

> 가지치기 없이 했을 경우 1 ~ 1.5초쯤 걸렸다.

## Furthermore
공식 사이트의 [Solution](https://leetcode.com/problems/longest-palindromic-substring/solution/)을 보니, *O(n)* 복잡도가 나오는 [Manacher's Algorithm](https://en.wikipedia.org/wiki/Longest_palindromic_substring#Manacher's_algorithm)가 있단다. 추후에 공부해보면 좋을 것 같다.