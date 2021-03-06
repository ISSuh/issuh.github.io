---
title: Programmers - 네트워크
date: 2020-02-02 12:54:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- DFS
- BFS
---

### [ 네트워크 ](https://programmers.co.kr/learn/courses/30/lessons/43162) - lv.3

#### 문제
네트워크란 컴퓨터 상호 간에 정보를 교환할 수 있도록 연결된 형태를 의미합니다. 예를 들어, 컴퓨터 A와 컴퓨터 B가 직접적으로 연결되어있고, 컴퓨터 B와 컴퓨터 C가 직접적으로 연결되어 있을 때 컴퓨터 A와 컴퓨터 C도 간접적으로 연결되어 정보를 교환할 수 있습니다. 따라서 컴퓨터 A, B, C는 모두 같은 네트워크 상에 있다고 할 수 있습니다.
컴퓨터의 개수 n, 연결에 대한 정보가 담긴 2차원 배열 computers가 매개변수로 주어질 때, 네트워크의 개수를 return 하도록 solution 함수를 작성하시오.

#### 제한사항
  - 컴퓨터의 개수 n은 1 이상 200 이하인 자연수입니다.
  - 각 컴퓨터는 0부터 n-1인 정수로 표현합니다.
  - i번 컴퓨터와 j번 컴퓨터가 연결되어 있으면 computers[i][j]를 1로 표현합니다.
  - computer[i][i]는 항상 1입니다.

#### 입출력 예
  
| n | vector | return |
| ----------- | ----------- | ----------- |
| 3 | [[1, 1, 0], [1, 1, 0], [0, 0, 1]] | 2 |
| 3 | [[1, 1, 0], [1, 1, 1], [0, 1, 1]] | 1 |

`예제 #1 아래와 같이 2개의 네트워크가 있습니다.`

![example](https://grepp-programmers.s3.amazonaws.com/files/ybm/5b61d6ca97/cc1e7816-b6d7-4649-98e0-e95ea2007fd7.png)

`예제 #2 아래와 같이 1개의 네트워크가 있습니다.`

![example](https://grepp-programmers.s3.amazonaws.com/files/ybm/7554746da2/edb61632-59f4-4799-9154-de9ca98c9e55.png)

#### 풀이
  - DFS, BFS

```cpp
int solution(int n, vector<vector<int>> computers) {
    int answer = 0;   
    stack<int> s;

    for(auto index = 0 ; index < n ; ++index){
        if(!computers[index][index])
            continue;
        
        s.push(index);

        while(!s.empty()){
            int i = s.top();
            s.pop();

            for(auto j = 0 ; j < n ; ++j){                
                if(computers[i][j] && computers[j][i]){
                    computers[i][j] = 0;
                    s.push(j);
                }
            }
        }
        
        ++answer;
    }
    
    return answer;
}
```