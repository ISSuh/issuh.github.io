---
title: Programmers - 줄 서는 방법
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Greedy
---

### [ 줄 서는 방법 ](https://programmers.co.kr/learn/courses/30/lessons/12936) - lv.3

#### 문제

n명의 사람이 일렬로 줄을 서고 있습니다. n명의 사람들에게는 각각 1번부터 n번까지 번호가 매겨져 있습니다. n명이 사람을 줄을 서는 방법은 여러가지 방법이 있습니다. 예를 들어서 3명의 사람이 있다면 다음과 같이 6개의 방법이 있습니다.

    [1, 2, 3]
    [1, 3, 2]
    [2, 1, 3]
    [2, 3, 1]
    [3, 1, 2]
    [3, 2, 1]

사람의 수 n과, 자연수 k가 주어질 때, 사람을 나열 하는 방법을 사전 순으로 나열 했을 때, k번째 방법을 return하는 solution 함수를 완성해주세요.


#### 제한사항
  - n은 20이하의 자연수 입니다.
  - k는 n! 이하의 자연수 입니다.

#### 입출력 예

| n | k| return |
| -- | -- | -- |
| 3 | 5 | [3,1,2] | 

#### 풀이
  - Greedy

```cpp
vector<int> solution(int n, long long k) {
    vector<int> answer;
    vector<int> item(n + 1, 1);
    vector<long long> mul(n + 1 ,1);

    // n 까지의 값과 n 까지의 factoral를 저장
    for(auto i = 1 ; i <= n ; ++i){
        item[i] = i;
        mul[i] = mul[i-1] * i;
    }
    
    // 구하고자하는 자리수의 값을 mul[i-1]으로 나눈 몫의 인덱스 값이 해당 자리의 값
    // 다음자리의 자리수 값은  현재 자리수 % mul[i-1] 임
    long long val = k - 1;
    for(auto i = n ; i > 1 ; --i){
        auto share = val / mul[i-1];
        val %= mul[i-1];        
        
        answer.push_back(*(item.begin() + 1 + share));
        item.erase(item.begin() + 1 + share);
    }
    
    answer.push_back(item.back());
    
    return answer;
}
```