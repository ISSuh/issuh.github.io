---
title: Programmers - 길 찾기 게임
date: 2020-02-02 11:15:00
categories: [Problem Solving, Programmers]
tags:
- PS
- Programmers
- Tree
---

### [ 길 찾기 게임 ](https://programmers.co.kr/learn/courses/30/lessons/42892) - lv.3

#### 문제

전무로 승진한 라이언은 기분이 너무 좋아 프렌즈를 이끌고 특별 휴가를 가기로 했다. 
내친김에 여행 계획까지 구상하던 라이언은 재미있는 게임을 생각해냈고 역시 전무로 승진할만한 인재라고 스스로에게 감탄했다.
라이언이 구상한(그리고 아마도 라이언만 즐거울만한) 게임은, 카카오 프렌즈를 두 팀으로 나누고, 각 팀이 같은 곳을 다른 순서로 방문하도록 해서 먼저 순회를 마친 팀이 승리하는 것이다.
그냥 지도를 주고 게임을 시작하면 재미가 덜해지므로, 라이언은 방문할 곳의 2차원 좌표 값을 구하고 각 장소를 이진트리의 노드가 되도록 구성한 후, 순회 방법을 힌트로 주어 각 팀이 스스로 경로를 찾도록 할 계획이다.
라이언은 아래와 같은 특별한 규칙으로 트리 노드들을 구성한다.
  - 트리를 구성하는 모든 노드의 x, y 좌표 값은 정수이다.
  - 모든 노드는 서로 다른 x값을 가진다.
  - 같은 레벨(level)에 있는 노드는 같은 y 좌표를 가진다.
  - 자식 노드의 y 값은 항상 부모 노드보다 작다.
  - 임의의 노드 V의 왼쪽 서브 트리(left subtree)에 있는 모든 노드의 x값은 V의 x값보다 작다.
  - 임의의 노드 V의 오른쪽 서브 트리(right subtree)에 있는 모든 노드의 x값은 V의 x값보다 크다.
  
아래 예시를 확인해보자.
라이언의 규칙에 맞게 이진트리의 노드만 좌표 평면에 그리면 다음과 같다. (이진트리의 각 노드에는 1부터 N까지 순서대로 번호가 붙어있다.)

![example](https://grepp-programmers.s3.amazonaws.com/files/production/dbb58728bd/a5371669-54d4-42a1-9e5e-7466f2d7b683.jpg)

이제, 노드를 잇는 간선(edge)을 모두 그리면 아래와 같은 모양이 된다.

![example](https://grepp-programmers.s3.amazonaws.com/files/production/dbb58728bd/a5371669-54d4-42a1-9e5e-7466f2d7b683.jpg)

위 이진트리에서 전위 순회(preorder), 후위 순회(postorder)를 한 결과는 다음과 같고, 이것은 각 팀이 방문해야 할 순서를 의미한다.
  - 전위 순회 : 7, 4, 6, 9, 1, 8, 5, 2, 3
  - 후위 순회 : 9, 6, 5, 8, 1, 4, 3, 2, 7
  
다행히 두 팀 모두 머리를 모아 분석한 끝에 라이언의 의도를 간신히 알아차렸다.

그러나 여전히 문제는 남아있다. 노드의 수가 예시처럼 적다면 쉽게 해결할 수 있겠지만, 예상대로 라이언은 그렇게 할 생각이 전혀 없었다.
이제 당신이 나설 때가 되었다.
곤경에 빠진 카카오 프렌즈를 위해 이진트리를 구성하는 노드들의 좌표가 담긴 배열 nodeinfo가 매개변수로 주어질 때, 
노드들로 구성된 이진트리를 전위 순회, 후위 순회한 결과를 2차원 배열에 순서대로 담아 return 하도록 solution 함수를 완성하자.

#### 제한사항
  - nodeinfo는 이진트리를 구성하는 각 노드의 좌표가 1번 노드부터 순서대로 들어있는 2차원 배열이다.
      - nodeinfo의 길이는 1 이상 10,000 이하이다.
      - nodeinfo[i] 는 i + 1번 노드의 좌표이며, [x축 좌표, y축 좌표] 순으로 들어있다.
      - 모든 노드의 좌표 값은 0 이상 100,000 이하인 정수이다.
      - 트리의 깊이가 1,000 이하인 경우만 입력으로 주어진다.
      - 모든 노드의 좌표는 문제에 주어진 규칙을 따르며, 잘못된 노드 위치가 주어지는 경우는 없다.

#### 입출력 예

| nodeinfo | return |
| -- | -- |
| [[5,3],[11,5],[13,3],[3,5],[6,1],[1,3],[8,6],[7,2],[2,2]] | [[7,4,6,9,1,8,5,2,3],[9,6,5,8,1,4,3,2,7]] |

#### 풀이1
  - Tree

```cpp
class TreeNode{
    friend class Tree;
public:
    TreeNode(int val = 0, int x = 0, int y = 0, TreeNode* left = nullptr, TreeNode* right = nullptr)
     : val(val), x(x), y(y), left(left), right(right) {}
    
private:
    int val;
    int x, y;
    TreeNode* left;
    TreeNode* right;
};

class Tree{
public:
    Tree(int data = 0, int x = 0, int y = 0) : root(new TreeNode(data, x, y)) {}
    
    void insert(TreeNode* node){
        TreeNode* parentNode = nullptr;
        TreeNode* currentNode = root;

        // 삽입할 노드의 부모노드를 찾기위해,
        // 현재가리키는 노드가 리프노드가 될때 까지 검사
        while (currentNode != nullptr) {
            parentNode = currentNode;
            
            // 삽입할 노드의 x좌표가 현재 가리키는 부모노드 보다 작으면 left
            // 삽입할 노드의 x좌표가 현재 가리키는 부모노드 보다 크면 right
            if (node->x < parentNode->x) 
                currentNode = currentNode->left;
            else 
                currentNode = currentNode->right;
        }
        
        // 부모노드를 찾았으므로 조건을 검사하여 left, right위치에 삽입
        if (node->x < parentNode->x) 
            parentNode->left = node;
        else 
            parentNode->right = node;
    }
    
    // PreOrder 조회
    void preOrder(vector<int>& ans, const TreeNode* const node){
        if(node == nullptr)
            return;
        
        ans.push_back(node->val);
        preOrder(ans, node->left);
        preOrder(ans, node->right);
    }
    
    // PostOrder 조회
    void postOrder(vector<int>& ans, const TreeNode* const node){
        if(node == nullptr)
            return;
        
        postOrder(ans, node->left);
        postOrder(ans, node->right);
        ans.push_back(node->val);
    }
    
    const TreeNode* getRoot() const { return root; }
    
private:
    TreeNode* root;
};

bool comp(vector<int>& a, vector<int>& b){
    if(a[1] == b[1])
        return a[0] < b[0];
    return a[1] > b[1];
}

vector<vector<int>> solution(vector<vector<int>> nodeinfo) {
    vector<vector<int>> answer(2);
    vector<vector<int>> item;
    
    // 각 노드의 번호 삽입
    for(auto i = 0 ; i < nodeinfo.size() ; ++i)
        item.push_back({nodeinfo[i][0], nodeinfo[i][1], i+1});
    
    // 좌표순으로 정렬
    sort(item.begin(), item.end(), comp);
    
    // 트리에 삽입
    Tree tree(item[0][2], item[0][0], item[0][1]);
    for(auto i = 1 ; i < item.size() ; ++i){
        tree.insert(new TreeNode(item[i][2], item[i][0], item[i][1]));
    }
    
    // 조회
    tree.preOrder(answer[0], tree.getRoot());
    tree.postOrder(answer[1], tree.getRoot());

    return answer;
}
```