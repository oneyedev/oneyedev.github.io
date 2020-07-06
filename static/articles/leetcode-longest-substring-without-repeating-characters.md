# Leetcode - Longest Substring Without Repeating Characters
leetcode의 [Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/) 문제이다.
난이도: medium

## Problem
주어진 문자열의 서브스트링 중 반복되는 문자가 없는 가장 긴 문자열의 길이를 찾는 문제이다. 

```sh
Input: "abcabcbb"
Output: 3 
Explanation: The answer is "abc", with the length of 3. 
```

```sh
Input: "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

```sh
Input: "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3. 
             Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

## How to solve
반복되는 문자가 없는 가장 긴 문자열을 줄여서 LUS(the Longest Unique Substring)라고 하자. 문제를 잘게 쪼개어 점화식으로 접근해보았다. 

```sh
answer(n): 0 ~ n번째까지의 문자열 중 LUS의 길이 
end(n): 0 ~ n번째까지의 문자열 중 n번째 문자를 포함하는 LUS(E-LUS)의 시작 위치
```
라고 하면
```sh
answer(n) = max(answer(n-1), n - end(n) + 1)
```
가 성립한다. 다시
```sh
index(n): 0 ~ n-1번째 문자열에서 n번째 문자의 위치 중 가장 큰 값, 즉 n번째 문자가 가장 최근에 발견된 위치
```
라 하면, _end(n)_의 값은 바로 이전 LUS의 범위내에 n번째 문자가 포함되어 있는지 여부로 알아낼 수 있다. 즉,
```sh
end(n) = index(n) + 1, If index(n) is greater or equals than end(n-1). 
end(n) = end(n-1), otherwise 
```
이다.

`index(n)`은 해시맵으로 유지하고, 점화식은 `n`과 `n-1`만으로 이루어져 있으므로 배열을 크게 유지 할 필요는 없어 보인다. python의 해시맵 기능을 담당하는 `dictionary`와 `sliding window` 알고리즘을 이용해 문제를 풀었다. 시간복잡도는 *O(n)* 쯤 될 듯.

## Solution
```py
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        arr = [x for x in s]
        N = len(arr)
        if N == 0:
            return 0
        answer = 1
        end = 0
        dic = {arr[0]: 0}
        for n in range(1, N):
            # The n-th number
            number = arr[n]

            # The last index of number
            index = dic.get(number)

            # If the last E-LUS contains number, Assign new value.
            # Otherwise keep previous value
            if index is not None and index >= end:
                end = index + 1

            # Update answer when the length of E-LUS is greater than previous answer
            answer = max(answer, n - end + 1)

            # Update last index of n-th number
            dic[number] = n
        return answer
```

## Result
Success
Runtime: 64 ms (상위 7~8% 쯤)
Memory Usage: 13.6 MB