---
title: Programmers - 방문 길이
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Brute Force
---

### [ 방문 길이 ](https://programmers.co.kr/learn/courses/30/lessons/49994#) - lv.3

#### 문제

게임 캐릭터를 4가지 명령어를 통해 움직이려 합니다. 명령어는 다음과 같습니다.
U: 위쪽으로 한 칸 가기
D: 아래쪽으로 한 칸 가기
R: 오른쪽으로 한 칸 가기
L: 왼쪽으로 한 칸 가기
캐릭터는 좌표평면의 (0, 0) 위치에서 시작합니다. 좌표평면의 경계는 왼쪽 위(-5, 5), 왼쪽 아래(-5, -5), 오른쪽 위(5, 5), 오른쪽 아래(5, -5)로 이루어져 있습니다.

![example](https://res.cloudinary.com/jistring93/image/upload/v1495542181/방문길이1_qpp9l3.png)

예를 들어, ULURRDLLU로 명령했다면

![example](https://res.cloudinary.com/jistring93/image/upload/v1495542443/방문길이2_lezmdo.png)

- 1번 명령어부터 7번 명령어까지 다음과 같이 움직입니다.

![example](https://res.cloudinary.com/jistring93/image/upload/v1495542704/방문길이3_sootjd.png)

- 8번 명령어부터 9번 명령어까지 다음과 같이 움직입니다.

![example](https://res.cloudinary.com/jistring93/image/upload/v1495542767/방문길이4_hlpiej.png)

이때, 우리는 게임 캐릭터가 지나간 길 중 캐릭터가 처음 걸어본 길의 길이를 구하려고 합니다. 예를 들어 위의 예시에서 게임 캐릭터가 움직인 길이는 9이지만, 캐릭터가 처음 걸어본 길의 길이는 7이 됩니다. (8, 9번 명령어에서 움직인 길은 2, 3번 명령어에서 이미 거쳐 간 길입니다)
단, 좌표평면의 경계를 넘어가는 명령어는 무시합니다.
예를 들어, LULLLLLLU로 명령했다면

![example](https://res.cloudinary.com/jistring93/image/upload/v1495545063/방문길이5_nitjwj.png)

- 1번 명령어부터 6번 명령어대로 움직인 후, 7, 8번 명령어는 무시합니다. 다시 9번 명령어대로 움직입니다.

![example](https://res.cloudinary.com/jistring93/image/upload/v1495544946/방문길이6_nzhumd.png)

이때 캐릭터가 처음 걸어본 길의 길이는 7이 됩니다.
명령어가 매개변수 dirs로 주어질 때, 게임 캐릭터가 처음 걸어본 길의 길이를 구하여 return 하는 solution 함수를 완성해 주세요.

#### 제한사항
  - dirs는 string형으로 주어지며, 'U', 'D', 'R', 'L' 이외에 문자는 주어지지 않습니다.
  - dirs의 길이는 500 이하의 자연수입니다.

#### 입출력 예

| dirs | return |
| -- | -- |
| "ULURRDLLU" | 7 | 
| "LULLLLLLU" | 7 | 

```
입출력 예 #1
문제의 예시와 같습니다.

입출력 예 #2
문제의 예시와 같습니다.
```

#### 풀이
  - Brute Force

```cpp
int solution(string dirs) {
	int answer = 0;
    int len = dirs.size(); 
    set<pair<pair<int,int>,pair<int,int>>> path;
    pair<int, int> now = {0, 0};
    
    // 현재좌표 -> 다음좌표 / 다음좌표 -> 현재좌표
    // 이 두가지 경로를 저장하여 지나온길인지, 새로운길인지 판단
    for(auto i = 0 ; i < len ; ++i){
        // 다음좌표값을 구함
        auto next = now;
        if(dirs[i] == 'U' && now.second+1 <= 5){
            next = {now.first, now.second+1};
        }
        else if(dirs[i] == 'D' && -5 <= now.second-1){
            next = {now.first, now.second-1};
        }
        else if(dirs[i] == 'L' && -5 <= now.first-1){
            next = {now.first-1, now.second};
        }
        else if(dirs[i] == 'R' && now.first+1 <= 5){
            next = {now.first+1, now.second};
        }
        
        // 현재좌표와 다음좌표가 같으면 좌표를 전체좌표에 벗어난 것이므로 무시
        if(now == next)
            continue; 
        
        // 현재좌표 -> 다음좌표 / 다음좌표 -> 현재좌표
        // 이 두가지 경우가 모두 없다면 새로 지나온길
        if(path.find({now, next}) == path.end() && path.find({next, now}) == path.end()){
            ++answer;

            path.insert({now, next});
            path.insert({next, now});
        }
        
        // 다음좌표값을 현재좌표로 저장
        now = next;
    }
        
    return answer;
}
```
