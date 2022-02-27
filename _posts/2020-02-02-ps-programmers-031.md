---
title: Programmers - 종이접기
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Array
---

### [ 종이접기 ](https://programmers.co.kr/learn/courses/30/lessons/62049?language=cpp) - lv.3

#### 문제

직사각형 종이를 n번 접으려고 합니다. 이때, 항상 오른쪽 절반을 왼쪽으로 접어 나갑니다. 다음은 n = 2인 경우의 예시입니다.

![example](https://res.cloudinary.com/dpxurmkij/image/upload/c_scale,w_390/v1500952547/%EC%A2%85%EC%9D%B4%EC%A0%91%EA%B8%B01_swcvrz.png)

먼저 오른쪽 절반을 왼쪽으로 접습니다.

![example](https://res.cloudinary.com/dpxurmkij/image/upload/c_scale,w_195/v1500952547/%EC%A2%85%EC%9D%B4%EC%A0%91%EA%B8%B02_e49oe3.png)

다시 오른쪽 절반을 왼쪽으로 접습니다.

![example](https://res.cloudinary.com/dpxurmkij/image/upload/c_scale,w_95/v1500952178/%EC%A2%85%EC%9D%B4%EC%A0%91%EA%B8%B03_nqdurc.png)

종이를 모두 접은 후에는 종이를 전부 펼칩니다. 종이를 펼칠 때는 종이를 접은 방법의 역순으로 펼쳐서 처음 놓여있던 때와 같은 상태가 되도록 합니다. 위와 같이 두 번 접은 후 종이를 펼치면 아래 그림과 같이 종이에 접은 흔적이 생기게 됩니다.

![example](https://res.cloudinary.com/dpxurmkij/image/upload/c_scale,w_390/v1500952178/%EC%A2%85%EC%9D%B4%EC%A0%91%EA%B8%B04_qxfoxr.png)

위 그림에서 ∨ 모양이 생긴 부분은 점선(0)으로, ∧ 모양이 생긴 부분은 실선(1)으로 표시했습니다.

종이를 접은 횟수 n이 매개변수로 주어질 때, 종이를 절반씩 n번 접은 후 모두 펼쳤을 때 생기는 접힌 부분의 모양을 배열에 담아 return 하도록 solution 함수를 완성해주세요.

#### 제한사항
  - 종이를 접는 횟수 n은 1 이상 20 이하의 자연수입니다.
  - 종이를 접었다 편 후 생긴 굴곡이 ∨ 모양이면 0, ∧ 모양이면 1로 나타냅니다.
  - 가장 왼쪽의 굴곡 모양부터 순서대로 배열에 담아 return 해주세요.

#### 입출력 예

| n | result |
| -- | --|
| 1 | [0] |
| 2 | [0,0,1] |
| 3 | [0,0,1,0,0,1,1] |

`입출력 예 #1 종이의 오른쪽 절반을 왼쪽으로 한번 접었다 펴면 아래 그림과 같이 굴곡이 생깁니다. `

![example](https://res.cloudinary.com/dpxurmkij/image/upload/c_scale,w_390/v1500952178/%EC%A2%85%EC%9D%B4%EC%A0%91%EA%B8%B05_fpgzni.png)

`입출력 예 #2 문제의 예시와 같습니다. `

`입출력 예 #3 종이를 절반씩 세 번 접은 후 다시 펼치면 아래 그림과 같이 굴곡이 생깁니다. `

![example](https://res.cloudinary.com/dpxurmkij/image/upload/c_scale,w_390/v1500952178/%EC%A2%85%EC%9D%B4%EC%A0%91%EA%B8%B06_sbn7li.png)

#### 풀이
  - Array

```cpp
vector<int> solution(int n) {
    vector<int> answer;
    int N = 0;
   
    for(auto i = 0 ; i < n ; ++i){
    	// 한번 접을때 마다 N*2+1만큼 증가
        N = N * 2 + 1;
        answer.resize(N);
     
        // 배열의 가운데는 무조건 0
        int size = answer.size() / 2;
        answer[size] = 0;
	
	// 인덱스 i 와 size()-i-1 의 값은 반대
        for(auto j = 0 ; j < size ; ++j){
            answer[answer.size() - j - 1] = answer[j] == 0 ? 1 : 0;
        }
    }
    
    return answer;
}
```
