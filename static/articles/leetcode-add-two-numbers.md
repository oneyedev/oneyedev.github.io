# Leetcode - Add Two Numbers
leetcode의 [Add Two Numbers](https://leetcode.com/problems/add-two-numbers) 문제이다.
난이도: medium

## Problem
역순으로 나오는 두 개의 양의 정수 `linked-list`를 각 자리수마다 합하여 결과 `linked-list`를 반환하는 함수를 구현해야한다.

```
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
```

## How to solve
딱히 이름있는 알고리즘이 사용되는 것은 아니지만 더한 값으로 인해 자리수가 올라가는 것을 고려해야하고 종료조건이 헷갈렸다. 시간복잡도는 *O(n)* 쯤 될 듯.

## Solution
```py
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def addTwoNumbers(self, l1: ListNode, l2: ListNode) -> ListNode:
        n1 = l1
        n2 = l2
        mok = 0
        node = None
        origin = None
        while n1 is not None or n2 is not None:
            v1 = n1.val if n1 is not None else 0
            v2 = n2.val if n2 is not None else 0
            sum = v1 + v2 + mok
            mok = sum // 10
            nam = sum % 10
            if node is None:
                node = ListNode(nam)
                origin = node
            else:
                node.next = ListNode(nam)
                node = node.next
            n1 = n1.next if n1 is not None else n1
            n2 = n2.next if n2 is not None else n2
        if mok > 0:
            node.next = ListNode(mok)
        return origin
```

## Result
Success
Runtime: 72 ms (상위 10% 쯤)
Memory Usage: 13.1 MB