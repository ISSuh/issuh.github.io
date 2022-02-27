---
title: Programmers - 가장 긴 팰린드롬
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- String
---

### [ 가장 긴 팰린드롬 ](https://programmers.co.kr/learn/courses/30/lessons/12904) - lv.3

#### 문제

앞뒤를 뒤집어도 똑같은 문자열을 팰린드롬(palindrome)이라고 합니다.
문자열 s가 주어질 때, s의 부분문자열(Substring)중 가장 긴 팰린드롬의 길이를 return 하는 solution 함수를 완성해 주세요.
예를들면, 문자열 s가 abcdcba이면 7을 return하고 abacde이면 3을 return합니다.

#### 제한사항
  - 문자열 s의 길이 : 2,500 이하의 자연수
  - 문자열 s는 알파벳 소문자로만 구성

#### 입출력 예

| s | return |
| -- | -- |
| "abcdcba" | 7 |
| "abacde" | 3 |

```
입출력 예 #1
	- 4번째자리 'd'를 기준으로 문자열 s 전체가 팰린드롬이 되므로 7을 return합니다.
입출력 예 #2
	- 2번째자리 'b'를 기준으로 aba가 팰린드롬이 되므로 3을 return합니다.
```

#### 풀이
  - String

```cpp
// 문자열의 길이가 홀수인 펠린드롬 찾기
int oddBasedPalindrome(const string& s){
    int answer = 1;
    int len = s.size();

    for(auto i = 1 ; i < len - 1 ; ++i){      
        // 인덱스 i를 기준으로 양옆의 문자가 같은지 판단
        int left = i - 1;
        int right = i + 1;
        int count = 1;
        
        // 양옆이 같으면 각 왼쪽, 오른쪽의 인덱스를
        // 1씩 증가시키면서 최대 펠린드롬 찾기
        while(left >= 0 && right < len){
            if(s[left] == s[right]){
                count += 2;
                --left;
                ++right;
            }
            else
                break;
        }
        
        answer = max(answer, count);
    }
    
    return answer;
}

// 문자열의 길이가 짝수인 펠린드롬 찾기
int evenBasedPalindrome(const string& s){
    int answer = 1;
    int len = s.size();

    for(auto i = 0 ; i < len - 1 ; ++i){
        // 인덱스 i를 기준으로 양옆의 문자가 같은지 판단
        int left = i;
        int right = i + 1;
        int count = 0;

        // 양옆이 같으면 각 왼쪽, 오른쪽의 인덱스를
        // 1씩 증가시키면서 최대 펠린드롬 찾기
        while(left >= 0 && right < len){
            if(s[left] == s[right]){
                count += 2;
                --left;
                ++right;
            }
            else
                break;
        }
        
        answer = max(answer, count);
    }
    
    return answer;
}

int solution(string s)
{
    int answer = max(evenBasedPalindrome(s), oddBasedPalindrome(s));
    return answer;
}
```