---
title: Programmers - 단어 변환
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- DFS
---

### [ 단어 변환 ](https://programmers.co.kr/learn/courses/30/lessons/43163) - lv.3

#### 문제

두 개의 단어 begin, target과 단어의 집합 words가 있습니다. 아래와 같은 규칙을 이용하여 begin에서 target으로 변환하는 가장 짧은 변환 과정을 찾으려고 합니다.

```
1. 한 번에 한 개의 알파벳만 바꿀 수 있습니다.
2. words에 있는 단어로만 변환할 수 있습니다.
```

예를 들어 begin이 "hit", target가 "cog", words가 ["hot","dot","dog","lot","log","cog"]라면 "hit" -> "hot" -> "dot" -> "dog" -> "cog"와 같이 4단계를 거쳐 변환할 수 있습니다.

두 개의 단어 begin, target과 단어의 집합 words가 매개변수로 주어질 때, 최소 몇 단계의 과정을 거쳐 begin을 target으로 변환할 수 있는지 return 하도록 solution 함수를 작성해주세요.

#### 제한사항
  - 각 단어는 알파벳 소문자로만 이루어져 있습니다.
  - 각 단어의 길이는 3 이상 10 이하이며 모든 단어의 길이는 같습니다.
  - words에는 3개 이상 50개 이하의 단어가 있으며 중복되는 단어는 없습니다.
  - begin과 target은 같지 않습니다.
  - 변환할 수 없는 경우에는 0를 return 합니다.

#### 입출력 예

| begin | target | words | return |
| -- | --| --| --|
| "hit" | "cog" | ["hot", "dot", "dog", "lot", "log", "cog"] | 4 |
| "hit" | "cog" | ["hot", "dot", "dog", "lot", "log"] | 0 |

`예제 #1 문제에 나온 예와 같습니다.`
`예제 #2 target인 "cog"는 words 안에 없기 때문에 변환할 수 없습니다.`

#### 풀이
  - DFS

```cpp
int solution(string begin, string target, vector<string> words) {
    int answer = INT_MAX;
    
    // begin과 하나만 다른words의 elements 찾아서 저장
    vector<string> choice;
    for(auto& str : words){
        int count = 0;
        for(auto i = 0 ; i < begin.size() ; ++i){
            if(begin[i] != str[i]){
                    ++count;
                }
            }
            if(count == 1)
                choice.push_back(str);
    }

    for(auto i = 0 ; i < choice.size() ; ++i){
        // 한번 조회한 element 체크
        vector<bool> check(words.size(), false);
        stack<pair<string, int>> s;
        int val = INT_MAX;
            
        s.push({choice[i], 1});
        
        while(!s.empty()){
            auto temp = s.top();
            s.pop();
            
            // target과 같을때, 현재 저장된 값보다 count가 작으면 저장
            if(temp.first == target){
                if(temp.second < val)
                    val = temp.second;
            }
            
            // 현재 단계에서 하나만 다른words의 elements 찾아서 저장
            for(auto j = 0; j < words.size() ; ++j){
                if(check[j])
                    continue;

                int count = 0;
                for(auto t = 0 ; t < words[j].size() ; ++t){
                    if(words[j][t] != temp.first[t]){
                        ++count;
                    }
                }
                if(count == 1){
                    s.push({words[j], temp.second + 1});
                    check[j] = true;
                }
            }
        }
        
        if(val < answer)
            answer = val;
    }
    
    return answer == INT_MAX ? 0 : answer;
}
```