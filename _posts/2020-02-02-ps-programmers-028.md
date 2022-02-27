---
title: Programmers - 정수 삼각형
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- DP
---

### [ 정수 삼각형 ](https://programmers.co.kr/learn/courses/30/lessons/43105) - lv.3

#### 문제

![example](https://grepp-programmers.s3.amazonaws.com/files/production/97ec02cc39/296a0863-a418-431d-9e8c-e57f7a9722ac.png)

위와 같은 삼각형의 꼭대기에서 바닥까지 이어지는 경로 중, 거쳐간 숫자의 합이 가장 큰 경우를 찾아보려고 합니다. 아래 칸으로 이동할 때는 대각선 방향으로 한 칸 오른쪽 또는 왼쪽으로만 이동 가능합니다. 예를 들어 3에서는 그 아래칸의 8 또는 1로만 이동이 가능합니다.

삼각형의 정보가 담긴 배열 triangle이 매개변수로 주어질 때, 거쳐간 숫자의 최댓값을 return 하도록 solution 함수를 완성하세요.

#### 제한사항
  - 삼각형의 높이는 1 이상 500 이하입니다.
  - 삼각형을 이루고 있는 숫자는 0 이상 9,999 이하의 정수입니다.

#### 입출력 예

| triangle | target |
| -- | -- | 
| [[7], [3, 8], [8, 1, 0], [2, 7, 4, 4], [4, 5, 2, 6, 5]] | 30 |

#### 풀이
  - DP

```cpp
int solution(vector<vector<int>> triangle) {
    vector<vector<int>> answer = triangle;
    
    for(auto i = 0 ; i < triangle.size() - 1 ; ++i ){
        for(auto j = 0 ; j < triangle[i].size() ; ++j){
            int left = answer[i][j] + triangle[i+1][j];
            int right = answer[i][j] + triangle[i+1][j+1];
            
            if(left > answer[i+1][j]){
                answer[i+1][j] = left;
            }
            
            if(right > answer[i+1][j+1]){
                answer[i+1][j+1] = right;
            }
        }
    }
    
    return *max_element(answer.rbegin()->begin(), answer.rbegin()->end());
}
```