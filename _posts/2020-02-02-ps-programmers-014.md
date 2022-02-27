---
title: Programmers - 큰 수 만들기
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Greedy
---

### [ 큰 수 만들기 ](https://programmers.co.kr/learn/courses/30/lessons/42883#) - lv.2

#### 문제

어떤 숫자에서 k개의 수를 제거했을 때 얻을 수 있는 가장 큰 숫자를 구하려 합니다.
예를 들어, 숫자 1924에서 수 두 개를 제거하면 [19, 12, 14, 92, 94, 24] 를 만들 수 있습니다. 이 중 가장 큰 숫자는 94 입니다.
문자열 형식으로 숫자 number와 제거할 수의 개수 k가 solution 함수의 매개변수로 주어집니다. number에서 k 개의 수를 제거했을 때 만들 수 있는 수 중 가장 큰 숫자를 문자열 형태로 return 하도록 solution 함수를 완성하세요.

### 제한사항
  - number는 1자리 이상, 1,000,000자리 이하인 숫자입니다.
  - k는 1 이상 number의 자릿수 미만인 자연수입니다.

### 입출력 예

| number | k | return |
| -- | -- | -- |
| "1924" | 2 | "94" |
| "1231234" | 3 | "3234" |
| "4177252841" | 4 | "775841" |

### 풀이
  - Greedy

```cpp
string solution(string number, int k) {
    int index = 1;
    
    while(k && index < number.size()){
        // index의 값과 index-1의 값을 비교하여
        // index-1값이 작으면 index-1의 원소를 제거
        // 제거후 전체 크기가 -1 만큼 줄어드므로,
        // 현재 index의 -1을 하여 재 비교가 될수 있도록 함
        if(index > 0 && number[index-1] < number[index]){
            number.erase(number.begin()+(index-1));
            --k;
            --index;
        }
        
        // 마지막 원소에서 까지 k만큼 제거가 안됬다면,
        // 마지막 원소부터 지우기 시작
        // 이때 index값과 index-1의 값을 비교하여 
        // index값이 작으면 index의 원소를 제거
        else if(index == number.size()-1 && number[index] <= number[index-1]){
            number.erase(number.begin()+index);
            --k;
            --index;
        }
        else{
            ++index;
        }        
    }
    
    return number;
}
```