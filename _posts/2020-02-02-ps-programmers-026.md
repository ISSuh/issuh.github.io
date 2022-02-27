---
title: Programmers - 디스크 컨트롤러
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Heap
---

### [ 디스크 컨트롤러 ](https://programmers.co.kr/learn/courses/30/lessons/42627#) - lv.3

#### 문제

하드디스크는 한 번에 하나의 작업만 수행할 수 있습니다. 디스크 컨트롤러를 구현하는 방법은 여러 가지가 있습니다. 가장 일반적인 방법은 요청이 들어온 순서대로 처리하는 것입니다.

예를들어

```
- 0ms 시점에 3ms가 소요되는 A작업 요청
- 1ms 시점에 9ms가 소요되는 B작업 요청
- 2ms 시점에 6ms가 소요되는 C작업 요청
```

와 같은 요청이 들어왔습니다. 이를 그림으로 표현하면 아래와 같습니다.

![eample](https://grepp-programmers.s3.amazonaws.com/files/production/b68eb5cec6/38dc6a53-2d21-4c72-90ac-f059729c51d5.png)

한 번에 하나의 요청만을 수행할 수 있기 때문에 각각의 작업을 요청받은 순서대로 처리하면 다음과 같이 처리 됩니다.

![eample](https://grepp-programmers.s3.amazonaws.com/files/production/5e677b4646/90b91fde-cac4-42c1-98b8-8f8431c52dcf.png)

```
- A: 3ms 시점에 작업 완료 (요청에서 종료까지 : 3ms)
- B: 1ms부터 대기하다가, 3ms 시점에 작업을 시작해서 12ms 시점에 작업 완료(요청에서 종료까지 : 11ms)
- C: 2ms부터 대기하다가, 12ms 시점에 작업을 시작해서 18ms 시점에 작업 완료(요청에서 종료까지 : 16ms)
```

이 때 각 작업의 요청부터 종료까지 걸린 시간의 평균은 10ms(= (3 + 11 + 16) / 3)가 됩니다.

하지만 A → C → B 순서대로 처리하면

![eample](https://grepp-programmers.s3.amazonaws.com/files/production/9eb7c5a6f1/a6cff04d-86bb-4b5b-98bf-6359158940ac.png)

```
- A: 3ms 시점에 작업 완료(요청에서 종료까지 : 3ms)
- C: 2ms부터 대기하다가, 3ms 시점에 작업을 시작해서 9ms 시점에 작업 완료(요청에서 종료까지 : 7ms)
- B: 1ms부터 대기하다가, 9ms 시점에 작업을 시작해서 18ms 시점에 작업 완료(요청에서 종료까지 : 17ms
```

이렇게 A → C → B의 순서로 처리하면 각 작업의 요청부터 종료까지 걸린 시간의 평균은 9ms(= (3 + 7 + 17) / 3)가 됩니다.

각 작업에 대해 [작업이 요청되는 시점, 작업의 소요시간]을 담은 2차원 배열 jobs가 매개변수로 주어질 때, 작업의 요청부터 종료까지 걸린 시간의 평균을 가장 줄이는 방법으로 처리하면 평균이 얼마가 되는지 return 하도록 solution 함수를 작성해주세요. (단, 소수점 이하의 수는 버립니다)

#### 제한사항
  - jobs의 길이는 1 이상 500 이하입니다.
  - jobs의 각 행은 하나의 작업에 대한 [작업이 요청되는 시점, 작업의 소요시간] 입니다.
  - 각 작업에 대해 작업이 요청되는 시간은 0 이상 1,000 이하입니다.
  - 각 작업에 대해 작업의 소요시간은 1 이상 1,000 이하입니다.
  - 하드디스크가 작업을 수행하고 있지 않을 때에는 먼저 요청이 들어온 작업부터 처리합니다.

#### 입출력 예

| jobs | return |
| -- | --| 
| [[0, 3], [1, 9], [2, 6]] | 9 |

```
예제 #1 
문제에 주어진 예와 같습니다.
    0ms 시점에 3ms 걸리는 작업 요청이 들어옵니다.
    1ms 시점에 9ms 걸리는 작업 요청이 들어옵니다.
    2ms 시점에 6ms 걸리는 작업 요청이 들어옵니다.
```

#### 풀이
  - Heap

```cpp
class Comp{
public:
    bool operator() (const vector<int>& lhs, const vector<int>& rhs) const{
        return lhs[1] > rhs[1];
    }
};

int solution(vector<vector<int>> jobs) {
    int answer = 0, curTime = 0, index = 0, runTime = 0;
    priority_queue<vector<int>, vector<vector<int>>, Comp> pq;
    
    sort(jobs.begin(), jobs.end());
    
    while(index < jobs.size() || !pq.empty()){
        // 현재 job의 끝나는 시간전 까지 요청이 시작되는 job들을 heap에 넣어,
        // 수행시간이 짧은 순으로(SJF) 정렬.
        while(index < jobs.size() && jobs[index][0] <= curTime){
            pq.push(jobs[index]);
            ++index;
        }
        
        // index는 아직 마지막이 아니지만 Heap이 비어있다면, 
        // 다음 job의 요청시간은 현재 job이 작업을 끝낸 이후.
        // 따라서 현재 시간을 다음 job의 요청시간으로 변경
        if(pq.empty()){
            curTime = jobs[index][0];
            continue;
        }

        // 현재 시간은 heap에 있는 최소 작업시간만큼 더한시간
        curTime += pq.top()[1];

        // 현재시간에 해당 job의 요청시간을 빼주어,
        // (요청시간 - 작업시간) 을 구함
        answer += curTime - pq.top()[0];

        pq.pop();
    }

    return answer / jobs.size();
}
```