---
title: simpleDB - 메모리
date: 2023-11-08 12:00:00 +0900
categories: [MyProject, simpleDB]
published: false
tags:
  - simpleDB
  - skiplist
---

# SkipList

구현하고 있는 simpleDB 프로젝트는 distribute key value storage 로,
기본적으로는 LSM-tree 를 사용하여 storage를 구현했고, 분산처리를 위해 Raft 알고리즘을 구현한 프로젝트이다.

여기서 LSM-tree 기반 storage를 구현하던 중 L0에 해당하는 메모리 영역에 데이터를 저장하기 위한 구조가 필요했었다.

![](https://d3i71xaburhd42.cloudfront.net/63c1a337908cf35784ad682e5c7d946f225bddc5/2-Figure1-1.png)

처음엔 단순히 map을 사용하여 메모리 영역인 L0을 구현했는데, 이왕 프로젝트 진행하는데 map은 너무 심심하다고 생각되어 적당한 데이터 구조가 없는지 여러 레퍼런스를 찾던도중 skiplist 가 적당할 것 같아 선택하게 되었다.

## SkipList 란?

위키에 skiplist를 검색하여 어떤건지 설명을 보면 아래와 같다.

> In computer science, a skip list (or skiplist) is a probabilistic data structure that allows O(log n) average complexity for search as well as O(log n) average complexity for insertion within an ordered sequence of n elements.

즉, skiplist는 linkedlist, tree와 같은 일종의 데이터구조의 한 종류로, 조회, 삽입의 시간복잡도가 O(log n)으로 예상되는 **정렬된** **확률적** 데이터구조이다.

왜 데이터구조에 확률적이라는 말이 붙었을까? 이는 skiplist의 기본적인 동작방식을 보면 이해할 수 있다.

### 기본 동작방식

skiplist는 기본적으로 여러 level이 존재하고 이 level은 linklist와 유사한 구조로 여러 node를 포함할 수 있도록 되어있다.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/86/Skip_list.svg/400px-Skip_list.svg.png)

![](https://upload.wikimedia.org/wikipedia/commons/thumb/2/2c/Skip_list_add_element-en.gif/400px-Skip_list_add_element-en.gif)

## 구현

```
# pseudocode

make all nodes level 1
j ← 1
while the number of nodes at level j > 1 do
    for each i'th node at level j do
        if i is odd and i is not the last node at level j
            randomly choose whether to promote it to level j+1
        else if i is even and node i-1 was not promoted
            promote it to level j+1
        end if
    repeat
    j ← j + 1
repeat
```

```go
type SkipList struct {
  // skiplist의 maxk level
  maxLevel int

  // 저장된 item 의 수
  length   int

  // 저장된 item(key, value)의 byte 크기
  size     uint64

  // node의 head, tail
  head     *SkipListNode
  tail     *SkipListNode

  // insert시 level 추론을 위한 rand
  rand     *rand.Rand

  // 조회한 node cache를 위한 임시 slice
  history  []*SkipListNode

  mutex    sync.RWMutex
}
```

---

참고

- https://en.wikipedia.org/wiki/Skip_list
