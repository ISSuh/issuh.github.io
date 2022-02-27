---
title: Programmers - 정수 내림차순으로 배치하기
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Math
---

### [ 정수 내림차순으로 배치하기 ](https://programmers.co.kr/learn/courses/30/lessons/12933) - lv.1

#### 문제

함수 solution은 정수 n을 매개변수로 입력받습니다. n의 각 자릿수를 큰것부터 작은 순으로 정렬한 새로운 정수를 리턴해주세요. 예를들어 n이 118372면 873211을 리턴하면 됩니다.

#### 제한사항
  - n은 1이상 8000000000 이하인 자연수입니다.

#### 입출력 예

| n | return |
|  -- |  -- |
| 118372 | 873211 |

#### 풀이
  - math

```cpp
long long solution(long long n) {
    long long answer = 0;
    long long temp = 10;
    vector<int> arr;
    
    while(temp < n * 10){
        arr.push_back(((n % temp) - (n % (temp/10))) / (temp/10));
        
        temp *= 10;
    }
    
    sort(arr.begin(), arr.end(), greater<int>());
    
    auto len = arr.size();
    temp = 1;
    for(auto i = 0 ; i < len ; ++i){
        answer += (arr.back() * temp);
        arr.pop_back();
        temp *= 10;
    }
    
    return answer;
}
```