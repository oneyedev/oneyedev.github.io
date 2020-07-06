# Leetcode - Container With Most Water
leetcode의 [Container With Most Water](https://leetcode.com/problems/container-with-most-water) 문제이다.
난이도: medium

## How to solve
단순한 접근은 *O(n^2)*으로, 그보다 빠른 알고리즘을 생각해내야 성공할 수 있는 것 같다.
다행히도 가지치기만으로도 Success가 가능했다.

```sh
left: 물을 담는 왼쪽 기둥의 후보 인덱스 배열
right: 물을 담는 오른쪽 기둥의 후보 인덱스 배열
```

이라고 정의하면

1. 임의의 `i`에 대해 `0` ~ `i-1`의 어떤 기둥 높이보다 낮을 경우 `i`는 `left`에 들 필요가 없다.
1. 임의의 `i`에 대해 `i+1` ~ `N`의 어떤 기둥 높이보다 낮을 경우 `i`는 `right`에 들 필요가 없다.

라고 할 수 있다(더 많은 물을 담을 수 있으므로).
이 두 규칙을 이용하여 가지치기 후 탐색했더니 성공하였다.

> 공식 홈페이지의 [Solution](https://leetcode.com/problems/container-with-most-water/solution/)을 보니 *O(n)*도 가능하다. 직관적으로는 쉬워보이고 코드도 굉장히 간단하나, 저 논리가 수학적으로 증명가능 하다는 걸 느끼기는 쉽지 않겠지... 수학 잘하는 사람들은 항상 부럽다.

## Solution
```py
from typing import List

class Solution:
    def maxArea(self, height: List[int]) -> int:
        N = len(height)
        left = []
        right = []
        maxLH = 0
        maxRH = 0
        for n in range(N):
            if height[n] > maxLH:
                left.append(n)
                maxLH = height[n]
            if height[N - n - 1] > maxRH:
                right.append(N - n - 1)
                maxRH = height[N - n - 1]
        area = 0
        for li in left:
            for ri in right:
                w = ri - li
                h = min(height[li], height[ri])
                area = max(area, w * h)
        return area
```

## Result
Success
Runtime: 60 ms (상위 20% 쯤)
Memory Usage: 14.5 MB