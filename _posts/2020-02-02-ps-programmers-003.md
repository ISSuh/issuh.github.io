---
title: Programmers - N-Queen
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Back Tracking
---

### [ N-Queen ](https://programmers.co.kr/learn/courses/30/lessons/12952) - lv.3

#### 문제

가로, 세로 길이가 n인 정사각형으로된 체스판이 있습니다. 체스판 위의 n개의 퀸이 서로를 공격할 수 없도록 배치하고 싶습니다.
예를 들어서 n이 4인경우 다음과 같이 퀸을 배치하면 n개의 퀸은 서로를 한번에 공격 할 수 없습니다.

![example](https://i.imgur.com/lt2zdK6.png)

![example](https://i.imgur.com/5c5EUrq.png)

체스판의 가로 세로의 세로의 길이 n이 매개변수로 주어질 때, n개의 퀸이 조건에 만족 하도록 배치할 수 있는 방법의 수를 return하는 solution함수를 완성해주세요.

#### 제한사항
  - 퀸(Queen)은 가로, 세로, 대각선으로 이동할 수 있습니다.
  - n은 12이하의 자연수 입니다.

#### 입출력 예

| n | return |
| -- | -- |
| 4 | 2 | 

```
입출력 예 #1
문제의 예시와 같습니다.
```

#### 풀이
  - Back Tracking

```cpp
void BackTracking(int& res, int n, vector<vector<bool>>& board, pair<int,int> point){
    // 열 index가 n과 같아지면 만족하는 경우를 찾았다는 뜻
    if(point.first == n){
        ++res;
        return;
    }
    
    // 현재 행에서 만족하는 좌표 검사 
    for(auto i = 0 ; i < n ; ++i){
        // 이전의 퀸에의해 해당 좌표가 가능한지 불가능한지 검사
        if(board[point.first][i]){
            auto check = board;
            check[point.first][i] = false;
            
            // 현재 좌표에 다음퀸이 올때, 보드에서 불가능한 부분 체크
            int right = i;
            int left = i;
            for(auto y = point.first + 1 ; y < n ; ++y){
                check[y][i] = false;

                if(right + 1 < n)
                    check[y][++right] = false;

                if(0 <= left - 1)
                    check[y][--left] = false;
            }

            // 퀸을 위치 시킨후 다음 행으로 이동
            BackTracking(res, n, check, {point.first+1, 0});
        }
    }
}

int solution(int n) {
    int answer = 0;
    vector<vector<bool>> board(n, vector<bool>(n, true));
    
    BackTracking(answer, n ,board, {0, 0});
        
    return answer;
}
```
