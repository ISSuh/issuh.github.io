---
title: Programmers - 섬 연결하기
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- DFS
- BFS
- MST
- Kruskal
---

### [ 섬 연결하기 ](https://programmers.co.kr/learn/courses/30/lessons/42861) - lv.3

#### 문제

n개의 섬 사이에 다리를 건설하는 비용(costs)이 주어질 때, 최소의 비용으로 모든 섬이 서로 통행 가능하도록 만들 때 필요한 최소 비용을 return 하도록 solution을 완성하세요.
다리를 여러 번 건너더라도, 도달할 수만 있으면 통행 가능하다고 봅니다. 예를 들어 A 섬과 B 섬 사이에 다리가 있고, B 섬과 C 섬 사이에 다리가 있으면 A 섬과 C 섬은 서로 통행 가능합니다.

#### 제한사항
  - 섬의 개수 n은 1 이상 100 이하입니다.
  - costs의 길이는 ((n-1) * n) / 2이하입니다.
  - 임의의 i에 대해, costs[i][0] 와 costs[i] [1]에는 다리가 연결되는 두 섬의 번호가 들어있고, costs[i] [2]에는 이 두 섬을 연결하는 다리를 건설할 때 드는 비용입니다.
  - 같은 연결은 두 번 주어지지 않습니다. 또한 순서가 바뀌더라도 같은 연결로 봅니다. 즉 0과 1 사이를 연결하는 비용이 주어졌을 때, 1과 0의 비용이 주어지지 않습니다.
  - 모든 섬 사이의 다리 건설 비용이 주어지지 않습니다. 이 경우, 두 섬 사이의 건설이 불가능한 것으로 봅니다.
  - 연결할 수 없는 섬은 주어지지 않습니다.

#### 입출력 예

| n | costs | return |
| -- | -- | -- |
| 4 | [[0,1,1],[0,2,2],[1,2,5],[1,3,1],[2,3,8]]	| 4 |

```
costs를 그림으로 표현하면 다음과 같으며, 이때 초록색 경로로 연결하는 것이 가장 적은 비용으로 모두를 통행할 수 있도록 만드는 방법입니다.
```
![example](https://grepp-programmers.s3.amazonaws.com/files/production/13e2952057/f2746a8c-527c-4451-9a73-42129911fe17.png)

#### 풀이
  - DFS, BFS, MST, Kruskal

```cpp
// cost가 작은순으로 정렬
bool comp(const vector<int>& a, const vector<int>& b){
    return a[2] < b[2];
}

// 부모노드 찾기
int getParent(vector<int>& parent, int node){
    if(parent[node] == node)
        return node;
    
    return parent[node] = getParent(parent, parent[node]);
}

// 작은 부모노드의 번호로 합치기
void unionParent(vector<int>& parent, int a, int b){
    a = getParent(parent, a);
    b = getParent(parent, b);
    
    int max = a > b ? a : b;
    int min = a <= b ? a : b;
    
    parent[max] = min;
}

// 부모노드 검사를 통해 사이클이 이루어지는지 검사
bool checkCycle(vector<int>& parent, int a, int b){
    a = getParent(parent, a);
    b = getParent(parent, b);
    
    if(a == b)  
        return true;
    else        
        return false;
}

int solution(int n, vector<vector<int>> costs) {
    int answer = 0;
    vector<vector<int>> g(n, vector<int>(n, 0));
    vector<int> parent(n, 0);

    for(auto i = 0 ; i < n ; ++i)
         parent[i] = i;
    
    sort(costs.begin(), costs.end(), comp);
    
    // kruskal 알고리즘을 통해 
    // minimum spanning tree(MST)를 구함
    for(auto i = 0 ; i < costs.size() ; ++i){
        if(!checkCycle(parent, costs[i][0], costs[i][1])){
            answer += costs[i][2];
            unionParent(parent, costs[i][0],costs[i][1]);
        }
    }

    return answer;
}
```