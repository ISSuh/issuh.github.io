---
title: Programmers - 야근 지수
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Greedy
---

### [ 야근 지수 ](https://programmers.co.kr/learn/courses/30/lessons/12927) - lv.3

#### 문제

회사원 Demi는 가끔은 야근을 하는데요, 야근을 하면 야근 피로도가 쌓입니다. 야근 피로도는 야근을 시작한 시점에서 남은 일의 작업량을 제곱하여 더한 값입니다. Demi는 N시간 동안 야근 피로도를 최소화하도록 일할 겁니다.Demi가 1시간 동안 작업량 1만큼을 처리할 수 있다고 할 때, 퇴근까지 남은 N 시간과 각 일에 대한 작업량 works에 대해 야근 피로도를 최소화한 값을 리턴하는 함수 solution을 완성해주세요.

#### 제한사항
  - works는 길이 1 이상, 20,000 이하인 배열입니다.
  - works의 원소는 50000 이하인 자연수입니다.
  - n은 1,000,000 이하인 자연수입니다.

#### 입출력 예

| works | n | return |
| -- | -- | -- |
| [4, 3, 3] | 4 | 12 |
| [2, 1, 2] | 1 | 6 |
| [1,1] | 3 | 0 |

#### 풀이
  - Greedy

```cpp
long long solution(int n, vector<int> works) {
    long long answer = 0;
    priority_queue<int, vector<int>, less<int>> pq;
    
    for(auto& i : works)
        pq.push(i);
    
    // n만큼 반복하며 현재 works중 가장 큰수를 골라 -1 함
    // 가장 큰수가 0 일땐 모든수가 0이므로 빠져나옴
    // priority queue를 이용하여 구성
    while(n--){
        auto max = pq.top();
        pq.pop();
        
        if(max == 0)
            break;
        
        pq.push(--max);
    }
    
    while(!pq.empty()){
        auto val = pq.top();
        pq.pop();
        
        answer += val * val;
    }
    
    return answer;
}
```