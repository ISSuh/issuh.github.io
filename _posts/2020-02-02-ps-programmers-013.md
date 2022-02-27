---
title: Programmers - 카카오프렌즈 컬러링북
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- DFS
- BFS
---

### [ 카카오프렌즈 컬러링북 ](https://programmers.co.kr/learn/courses/30/lessons/1829#) - lv.2

#### 문제

출판사의 편집자인 어피치는 네오에게 컬러링북에 들어갈 원화를 그려달라고 부탁하여 여러 장의 그림을 받았다. 여러 장의 그림을 난이도 순으로 컬러링북에 넣고 싶었던 어피치는 영역이 많으면 색칠하기가 까다로워 어려워진다는 사실을 발견하고 그림의 난이도를 영역의 수로 정의하였다. (영역이란 상하좌우로 연결된 같은 색상의 공간을 의미한다.)
그림에 몇 개의 영역이 있는지와 가장 큰 영역의 넓이는 얼마인지 계산하는 프로그램을 작성해보자.

![example](http://t1.kakaocdn.net/codefestival/apeach.png)

위의 그림은 총 12개 영역으로 이루어져 있으며, 가장 넓은 영역은 어피치의 얼굴면으로 넓이는 120이다.

#### 제한사항
  - 입력은 그림의 크기를 나타내는 m과 n, 그리고 그림을 나타내는 m × n 크기의 2차원 배열 picture로 주어진다. 
	- 1 <= m, n <= 100
	- picture의 원소는 0 이상 2^31 - 1 이하의 임의의 값이다.
	- picture의 원소 중 값이 0인 경우는 색칠하지 않는 영역을 뜻한다.

#### 입출력 예

| m | n | picture | return |
| -- | -- | -- | -- |
| 6 | 4 | [[1, 1, 1, 0], [1, 2, 2, 0], [1, 0, 0, 1], [0, 0, 0, 1], [0, 0, 0, 3], [0, 0, 0, 3]] | [4,5] |

#### 풀이
  - BFS, DFS

```cpp
vector<int> solution(int m, int n, vector<vector<int>> picture) {
    int number_of_area = 0;
    int max_size_of_one_area = 0;
    
    vector<vector<bool>> checker(m, vector<bool>(n, false));
    
    for(auto i = 0 ; i < m ; ++i){  
        for(auto j = 0 ; j < n ; ++j){
            // 현재 인덱스의 체커가 true 거나 색이 0 이면 패스
            if(checker[i][j] || !picture[i][j])
                continue;
            
            // BFS
            // 저장할 pair의 정보는 {행 index, 열 index}
            int count = 0;
            queue<pair<int, int>> q;
            q.push({i,j});
            
            while(!q.empty()){
                auto curPic = q.front();
                q.pop();
                                        
                // 이미 체크되어있거나, 색이 다르면 패스
                if(checker[curPic.first][curPic.second] || picture[curPic.first][curPic.second] != picture[i][j])
                    continue;
                
                // 현재 인덱스 체크 및 영역 수 증가
                checker[curPic.first][curPic.second] = true;
                count++;
                
                // 위 검사
                if(curPic.first + 1 < m)
                    q.push({curPic.first+1, curPic.second});
                // 아래 검사
                if(curPic.first - 1 >= 0) 
                    q.push({curPic.first-1, curPic.second});
                // 오른쪽 검사
                if(curPic.second + 1 < n)
                    q.push({curPic.first, curPic.second+1});
                // 왼쪽 검사
                if(curPic.second - 1 >= 0)
                    q.push({curPic.first, curPic.second-1});
            }
            
            ++number_of_area;
            max_size_of_one_area = max(count, max_size_of_one_area);
        }
    }
    
    return {number_of_area, max_size_of_one_area};
}
```