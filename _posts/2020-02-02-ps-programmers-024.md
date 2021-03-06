---
title: Programmers - 타일 장식물 
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- DP
---

### [  타일 장식물  ](https://programmers.co.kr/learn/courses/30/lessons/43104) - lv.3

#### 문제

대구 달성공원에 놀러 온 지수는 최근에 새로 만든 타일 장식물을 보게 되었다. 타일 장식물은 정사각형 타일을 붙여 만든 형태였는데, 한 변이 1인 정사각형 타일부터 시작하여 마치 앵무조개의 나선 모양처럼 점점 큰 타일을 붙인 형태였다. 타일 장식물의 일부를 그리면 다음과 같다.

![example](https://grepp-programmers.s3.amazonaws.com/files/production/3e31bedd54/fcc48066-e72f-45c8-af03-e4360b58b589.png)

그림에서 타일에 적힌 수는 각 타일의 한 변의 길이를 나타낸다. 타일 장식물을 구성하는 정사각형 타일 한 변의 길이를 안쪽 타일부터 시작하여 차례로 적으면 다음과 같다.
**[1, 1, 2, 3, 5, 8, .]**
지수는 문득 이러한 타일들로 구성되는 큰 직사각형의 둘레가 궁금해졌다. 예를 들어, 처음 다섯 개의 타일이 구성하는 직사각형(위에서 빨간색으로 표시한 직사각형)의 둘레는 26이다.

타일의 개수 N이 주어질 때, N개의 타일로 구성된 직사각형의 둘레를 return 하도록 solution 함수를 작성하시오.

#### 제한사항
  - N은 1 이상 80 이하인 자연수이다.

#### 입출력 예

| N | return |
| -- |  -- |
| 5 | 26 |
| 6 | 42 |

#### 풀이
  - DP

```cpp
long long solution(int N) {
    long long answer = 0;
    long long val = 1, preVal = 1;
    
    for(auto i = 2 ; i < N ; ++i){
        long long temp = val + preVal;
        preVal = val;
        val = temp;
    }
    
    answer = (val * 2) + ((val + preVal) * 2);
    return answer;
}
```