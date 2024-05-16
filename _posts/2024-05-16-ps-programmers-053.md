---
title: Programmers - 키패드 누르기
date: 2024-05-16 22:11:00
categories: [Problem Solving, Programmers]
published: true
tags:
  - PS
  - Programmers
  - SQL
---

### [ 한 해에 잡은 물고기 수 구하기 ](https://school.programmers.co.kr/learn/courses/30/lessons/298516) - lv.1

#### 문제

낚시앱에서 사용하는 FISH_INFO 테이블은 잡은 물고기들의 정보를 담고 있습니다. FISH_INFO 테이블의 구조는 다음과 같으며 ID, FISH_TYPE, LENGTH, TIME은 각각 잡은 물고기의 ID, 물고기의 종류(숫자), 잡은 물고기의 길이(cm), 물고기를 잡은 날짜를 나타냅니다.

| Column name | Type    | Nullable |
| ----------- | ------- | -------- |
| ID          | INTEGER | FALSE    |
| FISH_TYPE   | INTEGER | FALSE    |
| LENGTH      | FLOAT   | TRUE     |
| TIME        | DATE    | FALSE    |

단, 잡은 물고기의 길이가 10cm 이하일 경우에는 LENGTH 가 NULL 이며, LENGTH 에 NULL 만 있는 경우는 없습니다.

FISH_INFO 테이블에서 2021년도에 잡은 물고기 수를 출력하는 SQL 문을 작성해주세요.
이 때 컬럼명은 'FISH_COUNT' 로 지정해주세요.

#### 입출력 예

예시
예를 들어 FISH_INFO 테이블이 다음과 같다면

| ID  | FISH_TYPE | LENGTH | TIME       |
| --- | --------- | ------ | ---------- |
| 0   | 0         | 13.37  | 2021/12/04 |
| 1   | 0         | 50     | 2020/03/07 |
| 2   | 0         | 40     | 2020/03/07 |
| 3   | 1         | 43.33  | 2022/03/09 |
| 4   | 1         | NULL   | 2022/04/08 |
| 5   | 2         | NULL   | 2021/04/28 |

2021 년도에 잡은 물고기는 물고기의 ID 0, 5에 해당하는 물고기 2마리 입니다. 따라서 결과는 다음과 같아야 합니다.

| FISH_COUNT |
| ---------- |
| 2          |

#### 풀이

- SQL

```sql
SELECT
    COUNT(ID) as FISH_COUNT
FROM
    FISH_INFO
WHERE
    DATE(TIME) BETWEEN '2021-01-01' AND '2021-12-31';
```
