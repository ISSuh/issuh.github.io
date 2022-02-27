---
title: Programmers - 정수 제곱근 판별
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Math
---

### [ 정수 제곱근 판별 ](https://programmers.co.kr/learn/courses/30/lessons/12934) - lv.1

#### 문제

임의의 양의 정수 n에 대해, n이 어떤 양의 정수 x의 제곱인지 아닌지 판단하려 합니다.
n이 양의 정수 x의 제곱이라면 x+1의 제곱을 리턴하고, n이 양의 정수 x의 제곱이 아니라면 -1을 리턴하는 함수를 완성하세요.

#### 제한사항
  - n은 1이상, 50000000000000 이하인 양의 정수입니다.

#### 입출력 예

| n | return |
|  -- |  -- |
| 121 | 144 |
| 3 | -1|

```
입출력 예 #1
 - 121은 양의 정수 11의 제곱이므로, (11+1)를 제곱한 144를 리턴합니다.
입출력 예 #2
 - 3은 양의 정수의 제곱이 아니므로, -1을 리턴합니다.
```

#### 풀이
  - math

```cpp
long long solution(long long n) {
    long long answer = 0;
    long long mul = 1;
    
    while(mul <= n){
        ++answer;
        mul = answer * answer;
        
        if(n == mul){
            return (answer+1) * (answer+1);
        }
    }

    return -1;
}
```