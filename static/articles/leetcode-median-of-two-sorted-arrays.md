# Leetcode - Median of Two Sorted Arrays
leetcode의 [Median of Two Sorted Arrays](https://leetcode.com/problems/median-of-two-sorted-arrays) 문제이다.
난이도: hard

## Problem
정렬된 상태의 두 배열이 있다고 했을 때, 두 배열이 합쳐졌을 경우의 `중간값(median)`을 구하는 문제이다.
시간 복잡도가 _O(log(m+n))_이 되어야 한다.

```sh
nums1 = [1, 3]
nums2 = [2]

The median is 2.0
```
```sh
nums1 = [1, 2]
nums2 = [3, 4]

The median is (2 + 3)/2 = 2.5
```

## How to solve
_O(m+n)_이면 쉬운 문제 였겠지만, _O(log(m+n))_이라 한참을 고민하였다.
Solution을 보고 살짝 힌트를 얻어 풀어 내 나름대로 정리해본다.
우선 중간값을 기준으로 

1. 왼쪽의 요소개수 = 오른쪽의 요소개수
1. 왼쪽의 요소는 항상 오른쪽의 요소보다 작다.

라는 특성을 이용해야한다.

### 첫번째 조건 : "왼쪽의 요소개수 = 오른쪽의 요소개수"

```sh
a: 첫번째 배열에서 통합배열의 왼쪽/오른쪽을 나누는 인덱스(정확히는 왼쪽의 마지막 인덱스)
b: 두번째 배열에서 통합배열의 왼쪽/오른쪽을 나누는 인덱스(정확히는 왼쪽의 마지막 인덱스)
A: 첫번째 배열의 길이
B: 두번째 배열의 길이
```

라 하면 자연스레

```sh
a+1: 첫번째 배열에서 통합배열 왼쪽의 요소 개수
b+1: 두번째 배열에서 통합배열 왼쪽의 요소 개수
A-a-1: 첫번째 배열에서 통합배열 오른쪽의 요소 개수
B-b-1: 두번째 배열에서 통합배열 오른쪽의 요소 개수
```

가 되고 통합배열의 중간값을 기준으로 `왼쪽의 요소개수 = 오른쪽의 요소개수`이어야 하므로

```sh
(a+1) + (b+1) = (A-a-1) + (B-b-1)
```

이 된다. A와 B는 상수이고 a, b가 변수 이므로 b기준으로 정리하면

```sh
2(a+b) = A+B-4
b = (A+B)/2-a-2
```

가 된다. 즉, a의 값만 적절하게 찾아낼 수 있으면 b는 자연스레 따라온다는 뜻이다.

### 두번째 조건 : "왼쪽의 요소는 항상 오른쪽의 요소보다 작다."
적절한 a를 찾는 판별 조건은 두번째 특성 `왼쪽의 요소는 항상 오른쪽의 요소보다 작다.`을 이용해야 한다.

```sh
num(i): i 위치의 숫자
```
라 했을 때,

```sh
num(a) <= num(b+1)
num(b) <= num(a+1)
```
를 모두 만족해야 한다

> `num(a) <= num(a+1)`와 `num(b) <= num(b+1)`은 주어진 배열이 정렬된 상태이므로 이미 만족한다.

그러면 문제는 적절한 `a`를 찾기 위해 위의 조건을 이용하는 것으로 좁혀졌다. 단순히 `linear search`로 찾으면 구현은 단순하겠지만, 시간복잡도는 여전히 _O(m+n)_일 것이므로 `binary search`를 적용하였다.

위의 조건을 탐색자 관점에서 다시 바꾸면

```sh
num(a) > num(b+1) 인 경우, a는 작아져야 한다
num(b) > num(a+1) 인 경우, a는 커져야 한다(a와 b는 반비례 관계이므로).
```

가 된다.

## Solution
약간의 고려사항이 더 있기는 했지만(a를 찾은 후 중간값으로 어떻게 변환하는 등) 기본적인 구성은 위와 같다.

```py
from typing import List
import math

class Solution:
    def findMedianSortedArrays(self, nums1: List[int], nums2: List[int]) -> float:
        A = len(nums1)
        B = len(nums2)
        if A == 0 or B == 0:
            return self.getMedian(nums1 + nums2)
        T = (A + B) // 2
        da = A // 2
        a = 0
        while True:
            a += da
            b = T - a - 2
            numA = self.getNumber(nums1, a)
            nextA = self.getNumber(nums1, a + 1)
            numB = self.getNumber(nums2, b)
            nextB = self.getNumber(nums2, b + 1)
            if numA > nextB:
                da = -max(da // 2, 1)
            elif numB > nextA:
                da = +max(da // 2, 1)
            else:
                if (A + B) % 2 == 0:
                    return (max(numA, numB) + min(nextA, nextB)) / 2
                else:
                    return min(nextA, nextB)

    def getNumber(self, nums: List[int], index: int):
        if index < 0:
            return -math.inf
        elif index >= len(nums):
            return math.inf
        else:
            return nums[index]

    def getMedian(self, nums: List[int]):
        l = len(nums)
        m = l // 2
        n = l % 2
        return nums[m] if n == 1 else (nums[m - 1] + nums[m]) / 2
```

## Result
Success
Runtime: 56 ms (상위 6% 쯤)
Memory Usage: 13.5 MB