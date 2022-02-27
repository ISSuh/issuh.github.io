---
title: Programmers - 2 x n 타일링
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- DP
---

### [ 2 x n 타일링 ](https://programmers.co.kr/learn/courses/30/lessons/12900) - lv.3

#### 문제

가로 길이가 2이고 세로의 길이가 1인 직사각형모양의 타일이 있습니다. 이 직사각형 타일을 이용하여 세로의 길이가 2이고 가로의 길이가 n인 바닥을 가득 채우려고 합니다. 타일을 채울 때는 다음과 같이 2가지 방법이 있습니다.

    타일을 가로로 배치 하는 경우
    타일을 세로로 배치 하는 경우

예를들어서 n이 7인 직사각형은 다음과 같이 채울 수 있습니다.

![example](https://i.imgur.com/29ANX0f.png)

직사각형의 가로의 길이 n이 매개변수로 주어질 때, 이 직사각형을 채우는 방법의 수를 return 하는 solution 함수를 완성해주세요.

#### 제한사항
  - 가로의 길이 n은 60,000이하의 자연수 입니다.
  - 경우의 수가 많아 질 수 있으므로, 경우의 수를 1,000,000,007으로 나눈 나머지를 return해주세요.

#### 입출력 예

| n | result |
| -- | --|
| 4 | 5 |

`입출력 예 #1 다음과 같이 5가지 방법이 있다.`

![example](https://i.imgur.com/keiKrD3.png)
![example](https://i.imgur.com/O9GdTE0.png)
![example](https://i.imgur.com/IZBmc6M.png)
![example](https://i.imgur.com/29LWVzK.png)
![example](https://i.imgur.com/z64JbNf.png)

- 풀이
  - DP

```cpp
int solution(int n) {
    if(n == 0)
        return 0;
    
    int answer = 0;
    int n_1 = 1;
    int n_2 = 0;
    
    for(auto i = 1; i <= n ; ++i){
        answer = (n_2 + n_1) % 1000000007;
        n_2 = n_1;
        n_1 = answer;
    }
    
    return answer;
}
```