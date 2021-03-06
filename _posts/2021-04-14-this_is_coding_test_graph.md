---
title: "[이것이 취업을 위한 코딩 테스트다] 그래프 이론(Graph)"
category: Algorithm Theory
use_math: true
---

# 1. 복습

## 1) 그래프
> 노드와 간선의 정보를 가지고 있는 자료구조

- 구현 방법 : 인접 행렬 or 인접 리스트
    - 간선 정보 저장 시 메모리 복잡도(V : 노드 개수, E : 간선 개수)
        - 인접 행렬 : $O(V^2)$
        - 인접 리스트 : $O(E)$
    - 노드 A -> 노드 B 간선 비용 확인 시 시간 복잡도 비교
        - 인접 행렬 : $O(1)$
        - 인접 리스트 : $O(V)$

<br>

## 2) 트리
> 부모에서 자식으로 내려오는 계층적인 자료구조

- **그래프 자료구조의 한 종류**

<br>

## 3) 그래프 vs 트리

||그래프|트리|
|---|---|---|
|방향성|방향 그래프 or 무방향 그래프|방향 그래프|
|순환성|순환 and 비순환|비순환|
|루트 노드 존재 여부|루트 노드 없음|루트 노드 있음|
|노드간 관계성|부모-자식 관계 없음|부모-자식 관계|
|모델의 종류|네트워크 모델|계층 모델|

<br>

<br>

# 2. 서로소 집합(Disjoint Sets) 자료구조

## 1) 서로소 집합
- 공통 원소가 없는 집합

<br>

## 2) 서로소 집합 자료구조(= Union-find 자료구조)
> 서로소 부분 집합들로 나누어진 원소들의 데이터를 처리하기 위한 자료구조

- 서로소 집합은 자료구조는 두 가지 연산으로 조작할 수 있음
    - 합집합(Union) : 2개의 원소가 포함된 집합을 하나의 집합으로 합침
    - 찾기(Find) : 특정 원소가 속한 집합이 어떤 집합인지 알려줌
- 시간 복잡도 : $O(V+M(1+log_{2-M/V}V))$
    - $V = 노드 개수,\ V-1 = union\ 연산\ 수,\ M = find\ 연산\ 수$

<br>

## 3) 서로소 집합 계산 알고리즘(트리 자료구조 사용)

- 1. Union 연산 확인 후 서로 연결된 A, B 노드 확인<br>
    - I) A와 B의 루트 노드 A', B'를 찾음<br>
    - II) 번호가 작은 A'를 B'의 부모노드로 설정(B'가 A'를 가르키도록)

- 2. 모든 Union 연산을 처리할 때까지 1번 과정 반복
    
<br>

### (1) 간단한 동작 방식
- union의 관계를 효과적으로 보여주기 위해 그래프 형태로 시각화함<br>
  (실제로는 각 원소의 집합 정보를 표현할 때 트리 자료구조 사용함)
    
![서로소 집합 자료구조(Disjoint Sets)](/assets/images/posts/algorithm/this_is_coding_test/disjoint.png)

### (2) 알고리즘의 동작 과정

![서로소 집합 계산 알고리즘](/assets/images/posts/algorithm/this_is_coding_test/disjoint_ex.png)

<br>

#### 경로 압축(Path Compression)
> find 함수를 재귀적으로 호출하 부모 테이블 값을 갱신하는 기법

- 최악의 경우 find 함수가 모든 노드를 확인해야하는데, 시간복잡도가 $O(VM)$이 됨.
- 경로 압축(Path Compression) 기법을 적용해야 함
- 소스 코드

    ```python
    # 특정 원소가 속한 집합 찾기
    def find_parent(parent, x):
        # 루트 노드가 아니라면, 루트 노드를 찾을 때까지 재귀적으로 호출
    
        # 경로 압축 기법 적용
        if parent[x] != x:
            # 해당 노드의 루트 노드가 바로 부모 노드가 됨
            parent[x] = find_parent(parent, parent[x])
        return parent[x]
    
    # 두 원소가 속한 집합을 합치기
    def union_parent(parent, a, b):
        a = find_parent(parent, a)
        b = find_parent(parent, b)
        if a < b:
            parent[b] = a
        else:
            parent[a] = b
    
    # 노드의 개수와 간선(union 연산)의 개수 입력 받기
    v, e = map(int, input().split())
    # 6 4
    # 1 4
    # 2 3
    # 2 4
    # 5 6
    parent = [0] * (v + 1)
    
    # 부모 테이블상에서 부모를 자기 자신으로 초기화
    for i in range(1, v + 1):
        parent[i] = i
    
    # union 연산 수행
    for i in range(e):
        a, b = map(int, input().split())
        union_parent(parent, a, b)
    
    # 각 원소가 속한 집합 출력
    print('각 원소가 속한 집합 : ', end='')
    for i in range(1, v + 1):
        print(find_parent(parent, i), end=' ')
    
    print()
    
    # 부모 테이블 내용 출력
    print('부모 테이블 : ', end='')
    for i in range(1, v + 1):
        print(parent[i], end=' ')
    # 각 원소가 속한 집합 : 1 1 1 1 5 5
    # 부모 테이블 : 1 1 1 1 5 5
    ```

<br>

## 4) 서로소 집합을 활용한 사이클 판별
> 서로소 집합은 무향 그래프 내 **사이클을 판별**할 수 있음

<img src='/assets/images/posts/algorithm/this_is_coding_test/cycle.png' width=300 alt='서로소 집합 사이클'>

- 사이클(Cycle)
  - 연결된 노드들의 루트노드가 모두 같은 상황
  - 예 : 3개의 노드가 삼각형으로 연결된 상황
    
- 서로소 집합을 활용한 사이클 판별 방법
    - I) 각 간선을 확인하며 두 노드의 루트 노드를 확인
        - i) 루트 노드가 서로 다르다면 두 노드에 대해 union 연산 수행
        - ii) 루트 노드가 서로 같다면 사이클(Cycle)이 발생 한것!
    - II) 그래프에 포함되어 있는 모든 간선에 대해 I 반복

- 소스 코드

    ```python
    # 특정 원소가 속한 집합을 찾기
    def find_parent(parent, x):
        # 루트 노드가 아니라면, 루트 노드를 찾을 때까지 재귀적으로 호출
        if parent[x] != x:
            parent[x] = find_parent(parent, parent[x])
        return parent[x]
    
    # 두 원소가 속한 집합을 합치기
    def union_parent(parent, a, b):
        a = find_parent(parent, a)
        b = find_parent(parent, b)
        if a < b:
            parent[b] = a
        else:
            parent[a] = b
    
    # 노드의 개수와 간선(Union 연산)의 개수 입력 받기
    v, e = map(int, input().split())
    # 3 3
    # 1 2
    # 1 3
    # 2 3
    
    parent = [0] * (v + 1) # 부모 테이블 초기화하기
    
    # 부모 테이블상에서, 부모를 자기 자신으로 초기화
    for i in range(1, v + 1):
        parent[i] = i
    
    cycle = False # 사이클 발생 여부
    
    for i in range(e):
        a, b = map(int, input().split())
        # 사이클이 발생한 경우 종료
        if find_parent(parent, a) == find_parent(parent, b):
            cycle = True
            break
        # 사이클이 발생하지 않았다면 합집합(Union) 연산 수행
        else:
            union_parent(parent, a, b)
    
    if cycle:
        print("사이클이 발생했습니다.")
    else:
        print("사이클이 발생하지 않았습니다.")
    # 사이클이 발생했습니다.
    ```

<br>

# 3. 신장 트리(Spanning Tree)
> 하나의 그래프가 있을 때 모든 노드를 포함하면서 사이클이 존재하지 않는 부분 그래프

<img src='/assets/images/posts/algorithm/this_is_coding_test/spanning.png' width=500 alt='신장 트리(Spanning Tree)'>

### 크루스칼 알고리즘(Kruskal)
> 최소 비용으로 만들 수 있는 신장트리를 찾는 알고리즘(최소 신장 트리 알고리즘)

- 가장 적은 비용으로 모든 노드를 연결
- 그리드 알고리즘의 일종
- 방법
    1. 간선 데이터를 비용에 따라 오름차순 정렬
    2. 간선을 하나씩 확인하며 현재 간선이 사이클을 발생시키는지 확인(가장 짧은 간선부터)

        I.  사이클이 발생하지 않는 경우 최소 신장 트리에 포함(union함수 수행)

        II.  사이클이 발생하는 경우 최소 신장트리에 포함시키지 않음

    3. 모든 간선에 대하여 2번 과정 반복
- 시간 복잡도 : $O(ElogE),\ 간선 개수 = E$
- 예

![크루스칼 알고리즘(Kruskal)](/assets/images/posts/algorithm/this_is_coding_test/kruskal.png)

- 소스 코드

```python
# 특정 원소가 속한 집합을 찾기
def find_parent(parent, x):
    # 루트 노드가 아니라면, 루트 노드를 찾을 때까지 재귀적으로 호출
    if parent[x] != x:
        parent[x] = find_parent(parent, parent[x])
    return parent[x]

# 두 원소가 속한 집합을 합치기
def union_parent(parent, a, b):
    a = find_parent(parent, a)
    b = find_parent(parent, b)
    if a < b:
        parent[b] = a
    else:
        parent[a] = b

# 노드의 개수와 간선(Union 연산)의 개수 입력 받기
v, e = map(int, input().split())
# 7 9
parent = [0] * (v + 1) # 부모 테이블 초기화하기

# 모든 간선을 담을 리스트와, 최종 비용을 담을 변수
edges = []
result = 0

# 부모 테이블상에서, 부모를 자기 자신으로 초기화
for i in range(1, v + 1):
    parent[i] = i

# 모든 간선에 대한 정보를 입력 받기
for _ in range(e):
    a, b, cost = map(int, input().split())
    # 비용순으로 정렬하기 위해서 튜플의 첫 번째 원소를 비용으로 설정
    edges.append((cost, a, b))
# 1 2 29
# 1 5 75
# 2 3 35
# 2 6 34
# 3 4 7
# 4 6 23
# 4 7 13
# 5 6 53
# 6 7 25

# 간선을 비용순으로 정렬
edges.sort()

# 간선을 하나씩 확인하며
for edge in edges:
    cost, a, b = edge
    # 사이클이 발생하지 않는 경우에만 집합에 포함
    if find_parent(parent, a) != find_parent(parent, b):
        union_parent(parent, a, b)
        result += cost

print(result)
# 159
```

<br>

# 4. 위상 정렬(Topology Sort)