---
title: Programmers - 라면공장
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Heap
---

### [ 라면공장 ](https://programmers.co.kr/learn/courses/30/lessons/42629#) - lv.2

#### 문제

라면 공장에서는 하루에 밀가루를 1톤씩 사용합니다. 원래 밀가루를 공급받던 공장의 고장으로 앞으로 k일 이후에야 밀가루를 공급받을 수 있기 때문에 해외 공장에서 밀가루를 수입해야 합니다.
해외 공장에서는 향후 밀가루를 공급할 수 있는 날짜와 수량을 알려주었고, 라면 공장에서는 운송비를 줄이기 위해 최소한의 횟수로 밀가루를 공급받고 싶습니다.
현재 공장에 남아있는 밀가루 수량 stock, 밀가루 공급 일정(dates)과 해당 시점에 공급 가능한 밀가루 수량(supplies), 원래 공장으로부터 공급받을 수 있는 시점 k가 주어질 때, 밀가루가 떨어지지 않고 공장을 운영하기 위해서 최소한 몇 번 해외 공장으로부터 밀가루를 공급받아야 하는지를 return 하도록 solution 함수를 완성하세요.
dates[i]에는 i번째 공급 가능일이 들어있으며, supplies[i]에는 dates[i] 날짜에 공급 가능한 밀가루 수량이 들어 있습니다.

#### 제한사항
  - stock에 있는 밀가루는 오늘(0일 이후)부터 사용됩니다.
  - stock과 k는 2 이상 100,000 이하입니다.
  - dates의 각 원소는 1 이상 k 이하입니다.
  - supplies의 각 원소는 1 이상 1,000 이하입니다.
  - dates와 supplies의 길이는 1 이상 20,000 이하입니다.
  - k일 째에는 밀가루가 충분히 공급되기 때문에 k-1일에 사용할 수량까지만 확보하면 됩니다.
  - dates에 들어있는 날짜는 오름차순 정렬되어 있습니다.
  - dates에 들어있는 날짜에 공급되는 밀가루는 작업 시작 전 새벽에 공급되는 것을 기준으로 합니다. 예를 들어 9일째에 밀가루가 바닥나더라도, 10일째에 공급받으면 10일째에는 공장을 운영할 수 있습니다.
  - 밀가루가 바닥나는 경우는 주어지지 않습니다.

#### 입출력 예

| stock | dates |	supplies | k | return |
| -- | -- | -- | -- | -- |
| 4	| [4,10,15] | [20,5,10] | 30 | 2 |

```
입출력 예 #1
  - 현재 밀가루가 4톤 남아 있기 때문에 오늘과 1일 후~3일 후까지 사용하고 나면 모든 밀가루를 다 사용합니다. 따라서 4일 후에는 반드시 밀가루를 공급받아야 합니다.
  - 4일째 공급받고 나면 15일 이후 아침에는 9톤의 밀가루가 남아있게 되고, 이때 10톤을 더 공급받으면 19톤이 남아있게 됩니다. 15일 이후부터 29일 이후까지 필요한 밀가루는 15톤이므로 더 이상의 공급은 필요 없습니다.
  - 따라서 총 2회의 밀가루를 공급받으면 됩니다.
```

#### 풀이
  - Heap

```cpp
int solution(int stock, vector<int> dates, vector<int> supplies, int k) {
    int answer = 0;
    int index = 0; 
    priority_queue<int, vector<int>, less<int>> pq;
    
    // 하루씩 k일 까지 날짜를 증가시킴
    // heap을 통해, 보급받은 수량이 가장 큰 것을 선택해 나감 
    for(auto day = 0 ; day < k ; ++day){
        // 현재 날짜가 dates[index]의 날짜와 같다면
        // heap에 해당 날짜의 공급량을 저장하며 index를 하나 증가
        if(dates[index] == day){
            pq.push(supplies[index]);
            ++index;
        }
        
        // 현재 남아있는 수량이 바닥나면,
        // heap에 저장되어 있는 최대 수량만큼 채움고,
        // 보급받은 횟수 +1
        if(stock == 0){
            stock = pq.top();
            pq.pop();
            ++answer;
        }
        
        --stock;
    }
    
    return answer;
}
```