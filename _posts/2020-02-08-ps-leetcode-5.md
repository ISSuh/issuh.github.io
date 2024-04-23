---
title: LeetCode - 5. Longest Palindromic Substring
date: 2020-02-08 15:56:00
categories: [Problem Solving, LeetCode]
tags:
  - PS
  - Leetcode
  - String
  - Array
---

### [ 5. Longest Palindromic Substring ](https://leetcode.com/problems/longest-palindromic-substring/) - medium

#### 문제

Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.

#### 제한사항

#### 입출력 예

```
Example 1:
Input: "babad"
Output: "bab"
Note: "aba" is also a valid answer.

Example 2:
Input: "cbbd"
Output: "bb"
```

#### 풀이

- String, Array

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        if(s.size() < 2)
            return s;

        string answer = "";

        for(auto index = 1 ; index < s.size() ; ++index){
            string oddRes = {s[index]}, evenRes = {s[index]};
            int oddLeft = index - 1, oddRight = index + 1;
            int evenLeft = index - 1, evenRight = index;
            bool oddFlag = false, evenFlag = false;

            while(1){
                // 문자열의 길이가 홀수인 sub palindrome 검색
                // 문자열의 길이가 홀수 이므로 현재 index를 기준으로 잡고,
                // 양쪽의 index를 --left, ++right 하여 각 문자르 비교해 sub palindrome 탐색
                if((oddLeft >= 0 && oddRight < s.size()) && (s[oddLeft] == s[oddRight])){
                    oddRes = s.substr(oddLeft, oddRight - oddLeft + 1);
                    --oddLeft;
                    ++oddRight;
                }
                // 비교시 같지 않다면 트리거를 true로 변환
                else{
                    oddFlag = true;
                }

                // 문자열의 길이가 짝수인 sub palindrome 검색
                // 문자열의 길이가 짝수 이므로 초기 right의 index를 중앙 index로 기준을 잡고,
                // 양쪽의 index를 --left, ++right 하여 각 문자르 비교해 sub palindrome 탐색
                if((evenLeft >= 0 && evenRight < s.size()) && (s[evenLeft] == s[evenRight])){
                    evenRes = s.substr(evenLeft, evenRight - evenLeft + 1);
                    --evenLeft;
                    ++evenRight;
                }
                // 비교시 같지 않다면 트리거를 true로 변환
                else{
                   evenFlag = true;
                }

                // 짝수, 홀수 모두 트리거가 true면,
                // 현재 index기준으 sub palindrome 탐색완료
                if(oddFlag && evenFlag)
                    break;
            }

            // 문자열의 길이가 긴 palindrome을 선택
            auto sub =  oddRes.size() > evenRes.size() ? oddRes : evenRes;
            answer = answer.size() > sub.size() ? answer : sub;
        }

        return answer;
    }
};
```
