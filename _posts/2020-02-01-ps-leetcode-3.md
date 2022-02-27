---
title: LeetCode - 3. Longest Substring Without Repeating Characters
date: 2020-02-01 22:33:00
categories: [Problem Solving, LeetCode]
tags:
- PS
- Leetcode
- String
- Brute Force
- Hash
---

### [ 3. Longest Substring Without Repeating Characters ](https://leetcode.com/problems/longest-substring-without-repeating-characters/) - medium

#### 문제

Given a string, find the length of the longest substring without repeating characters.

#### 제한사항

#### 입출력 예

```
Example 1:
Input: "abcabcbb"
Output: 3 
Explanation: The answer is "abc", with the length of 3. 

Example 2:
Input: "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.

Example 3:
Input: "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3. 
             Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

#### 풀이
  - String, Brute Force, Hash

```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        if(s.size() == 1)
            return 1;
        
        set<char> sub;
        string subStr;
        
        // 문자열의 모든 경우를 검사하여 최대 길이를 검색
        for(auto i = 0 ; i < s.size(); ++i){
            for(auto j = i ; j < s.size() ; ++j){
                // Set에 현재 문자가 없으면 추가
                if(sub.find(s[j]) == sub.end()){
                    sub.insert(s[j]);
                }
                // Set에 현재 문자가 있으면
                // 현재까지의 문자열과 이전의 문자열과 길이를 비교하여 문자열 저장
                else{
                    string temp;
                    
                    for(auto& i : sub)
                        temp.push_back(i);
                    
                    if(subStr.size() < temp.size())
                        subStr = temp;
                    
                    sub.clear();

                    break;
                }
            }
        }
             
        return subStr.size();
    }
};
```