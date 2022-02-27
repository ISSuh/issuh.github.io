---
title: Programmers - [1차] 비밀지도
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Bit Manipulte
---

### [ [1차] 비밀지도 ](https://programmers.co.kr/learn/courses/30/lessons/17681) - lv.1

#### 문제

네오는 평소 프로도가 비상금을 숨겨놓는 장소를 알려줄 비밀지도를 손에 넣었다. 그런데 이 비밀지도는 숫자로 암호화되어 있어 위치를 확인하기 위해서는 암호를 해독해야 한다. 다행히 지도 암호를 해독할 방법을 적어놓은 메모도 함께 발견했다.
지도는 한 변의 길이가 n인 정사각형 배열 형태로, 각 칸은 공백(" ) 또는벽(#") 두 종류로 이루어져 있다.
전체 지도는 두 장의 지도를 겹쳐서 얻을 수 있다. 각각 지도 1과 지도 2라고 하자. 지도 1 또는 지도 2 중 어느 하나라도 벽인 부분은 전체 지도에서도 벽이다. 지도 1과 지도 2에서 모두 공백인 부분은 전체 지도에서도 공백이다.
지도 1과 지도 2는 각각 정수 배열로 암호화되어 있다.
암호화된 배열은 지도의 각 가로줄에서 벽 부분을 1, 공백 부분을 0으로 부호화했을 때 얻어지는 이진수에 해당하는 값의 배열이다.

![example](http://t1.kakaocdn.net/welcome2018/secret8.png)

네오가 프로도의 비상금을 손에 넣을 수 있도록, 비밀지도의 암호를 해독하는 작업을 도와줄 프로그램을 작성하라.

#### 제한사항
  - 1 ≦ n ≦ 16
  - arr1, arr2는 길이 n인 정수 배열로 주어진다.
  - 정수 배열의 각 원소 x를 이진수로 변환했을 때의 길이는 n 이하이다. 즉, 0 ≦ x ≦ 2n - 1을 만족한다.

#### 입출력 예

| 매개변수 | 값 |
| -- | -- |
| n | 5 |
| arr1 | [9, 20, 28, 18, 11] |
| arr2 | [30, 1, 21, 17, 28] |
| 출력 | ["#####","# # #", "### #", "# ##", "#####"] |

| 매개변수 | 값 |
| -- | -- |
| n | 6 |
| arr1 | [46, 33, 33 ,22, 31, 50]|
| arr2 | [27 ,56, 19, 14, 14, 10]|
| 출력 | ["######", "### #", "## ##", " #### ", " #####", "### # "]|

#### 풀이
  - Bit Manipulte

```cpp
vector<string> solution(int n, vector<int> arr1, vector<int> arr2) {
    vector<string> answer;
    vector<int> makeMap;
    
    // & 연산을 통해 두 지도 배열을 합침
    for(auto i = 0 ; i < n ; ++i){
        makeMap.push_back(arr1[i] | arr2[i]);
    }
    
    // 합친 지도에서 각 행마다 
    // 2진수를 조사하여 1의 위치를 알아냄
    for(auto& i : makeMap){
        string res;
        stack<int> s;
        
        // 2진수의 1의 위치를 조사할때,
        // 순서에 반대부터 조사하므로 stack을 이용하여 뒤집음
        for(auto k = 0 ; k < n ; ++k){
            auto temp = i & 1;
            s.push(temp);
            i >>= 1;
        }
        
        while(!s.empty()){
            if(s.top() == 1)
                res.push_back('#');
            else
                res.push_back(' ');
            
            s.pop();
        }
        
        answer.push_back(res);
    }
    
    return answer;
}
```