---
title: Programmers - 여행경로
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- DFS
- BFS
---

### [ 여행경로 ](https://programmers.co.kr/learn/courses/30/lessons/43164) - lv.3

#### 문제

주어진 항공권을 모두 이용하여 여행경로를 짜려고 합니다. 항상 "ICN" 공항에서 출발합니다.

항공권 정보가 담긴 2차원 배열 tickets가 매개변수로 주어질 때, 방문하는 공항 경로를 배열에 담아 return 하도록 solution 함수를 작성해주세요.

#### 제한사항
  - 모든 공항은 알파벳 대문자 3글자로 이루어집니다.
  - 주어진 공항 수는 3개 이상 10,000개 이하입니다.
  - tickets의 각 행 [a, b]는 a 공항에서 b 공항으로 가는 항공권이 있다는 의미입니다.
  - 주어진 항공권은 모두 사용해야 합니다.
  - 만일 가능한 경로가 2개 이상일 경우 알파벳 순서가 앞서는 경로를 return 합니다.
  - 모든 도시를 방문할 수 없는 경우는 주어지지 않습니다.

#### 입출력 예

| tickets | return |
| -- | --| 
| [["ICN", "JFK"], ["HND", "IAD"], ["JFK", "HND"]] | ["ICN", "JFK", "HND", "IAD"] |
| [["ICN", "SFO"], ["ICN", "ATL"], ["SFO", "ATL"], ["ATL", "ICN"], ["ATL","SFO"]] | ["ICN", "ATL", "ICN", "SFO", "ATL", "SFO"] |

`
예제 #1 
["ICN", "JFK", "HND", "IAD"] 순으로 방문할 수 있습니다.
`

`
예제 #2 
["ICN", "SFO", "ATL", "ICN", "ATL", "SFO"] 순으로 방문할 수도 있지만 ["ICN", "ATL", "ICN", "SFO", "ATL", "SFO"] 가 알파벳 순으로 앞섭니다.
`

#### 풀이
  - DFS, BFS

```cpp
vector<string> solution(vector<vector<string>> tickets) {
    vector<vector<string>> answer;
    vector<vector<int>> answerIndex;
    map<string, vector<pair<string, int>>> m;
    
    // tickets의 요소를 map으로 구성
    for(auto i = 0 ; i < tickets.size() ; ++i){
        m[tickets[i][0]].push_back({tickets[i][1], i});
    }
    
    // ICN부터 시작하여 요소를 DFS로 검사
    for(auto i = m["ICN"].begin() ; i != m["ICN"].end() ; ++i ){
        stack<pair<string, vector<int>>> q;
        q.push({i->first, {i->second}});

        while(!q.empty()){
            auto tmp = q.top();
            q.pop();

            if(tmp.second.size() == tickets.size()){
                answerIndex.push_back(tmp.second);
            }
            
            for(auto j = m[tmp.first].rbegin() ; j != m[tmp.first].rend() ; ++j){
                // 현재까지 저장된 index vector에 현재 index가 없는지 검사 
                if(find(tmp.second.begin(), tmp.second.end(), j->second) == tmp.second.end()){
                    // 현재 검사중인 운항출발지의 여러 목적지가 답이 될수 있으므로
                    // vector를 copy하여 이를 저장
                    auto ans = tmp.second;
                    ans.push_back(j->second);
                    q.push({j->first, ans});
                }
            }
        }
    }

    // 얻은 경로의 index를 기반하여 문자열 생성
    for(auto& i : answerIndex){
        vector<string> temp;
        temp.push_back(tickets[i[0]][0]);
        
        for(auto& j : i){
            temp.push_back(tickets[j][1]);
        }
        answer.push_back(temp);
    }

    // 알파벳 순으로 정렬
    sort(answer.begin(), answer.end());

    return answer[0];
}
```