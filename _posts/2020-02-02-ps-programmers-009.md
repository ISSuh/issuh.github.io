---
title: Programmers - 저울
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Greedy
---

### [ 저울 ](https://programmers.co.kr/learn/courses/30/lessons/42886#) - lv.3

#### 문제

하나의 양팔 저울을 이용하여 물건의 무게를 측정하려고 합니다. 이 저울의 양팔의 끝에는 물건이나 추를 올려놓는 접시가 달려 있고, 양팔의 길이는 같습니다. 또한, 저울의 한쪽에는 저울추들만 놓을 수 있고, 다른 쪽에는 무게를 측정하려는 물건만 올려놓을 수 있습니다.

![example](https://grepp-programmers.s3.amazonaws.com/files/production/f73e61d4de/f4abf5ff-1956-4e49-bd4a-d3d24619bbf0.png)

저울추가 담긴 배열 weight가 매개변수로 주어질 때, 이 추들로 측정할 수 없는 양의 정수 무게 중 최솟값을 return 하도록 solution 함수를 작성해주세요.
예를 들어, 무게가 각각 [3, 1, 6, 2, 7, 30, 1]인 7개의 저울추를 주어졌을 때, 이 추들로 측정할 수 없는 양의 정수 무게 중 최솟값은 21입니다.

#### 제한사항
  - 저울추의 개수는 1개 이상 10,000개 이하입니다.
  - 각 추의 무게는 1 이상 1,000,000 이하입니다.

#### 입출력 예

| weight | return |
| -- | -- |
| [3, 1, 6, 2, 7, 30, 1] | 21 |

#### 풀이
  - Greedy

```cpp
int solution(vector<int> weight) {
    int answer = 0;
    
    sort(weight.begin(), weight.end());
    answer = weight[0];
    
    // 정렬후, 요소들의 순서대로 합했을때, 표현할 수 있는 무게는
    // weight[0] ~ weight[i-1] 까지의 합 까지 표현 할수 있음
    // 즉,
    //
    // ((weight[0] ~ weight[i-1] 까지의 합) + 1) >= weight[i] 이면,
    // 초기값 ~ (weight[0] ~ weight[i-1] 까지의 합)의 무게를 모두 표현가능함
    //
    // ((weight[0] ~ weight[i-1] 까지의 합) + 1) < weight[i] 이면,
    // 초기값 ~ (weight[0] ~ weight[i-1] 까지의 합)과 weight[i]사이의 값은
    // 표현할 수 없다.
    for(auto i = 0 ; i < weight.size() ; ++i){
        if(answer+1 < weight[i]){
            break;
        }
        
        answer += weight[i];
    }
    
    return answer;
}
```