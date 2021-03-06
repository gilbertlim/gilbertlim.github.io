---
title: "[이것이 취업을 위한 코딩 테스트다] 정렬(Sorting)"
category: Algorithm Theory
use_math: true
---

## 정렬(Sorting)
- 데이터를 특정한 기준에 따라서 순서대로 나열하는 것
- 프로그램 작성 시 가장 많이 사용되는 알고리즘 
- 이진 탐색의 전처리 과정
- 정렬을 공부하다 보면'알고리즘의 효율성'을 쉽게 이해할 수 있게 됨
- 상황에 적절한 정렬 알고리즘을 사용할 수 있어야 함
- 오름 차순으로 정렬하는 방법만 다룰 예정임(내림차순은 오름차순 정렬된 결과를 Reverse하면 됨, $O(N)$) 

<br>

### 문제 유형
> 문제에서 별도의 요구사항이 없다면 단순히 정렬해야 하는 상황에서는 **기본 정렬 라이브러리**를 사용하고,
> 
> 데이터의 범위가 한정되어 있으며 더 빠르게 동작해야할 때는 계수정렬을 사용

1. **정렬 라이브러리**로 풀 수 있는 문제
   - 파이썬 정렬 라이브러리($O(NlogN)$)

2. **정렬 알고리즘의 원리**에 대해서 물어보는 문제(데이터를 비교하며 위치를 변경하는 정렬 방법)
   - **선택 정렬($O(N^2)$)**
   - **삽입 정렬($O(N^2)\ or\ O(N)$)**
   - **퀵 정렬($O(NlogN)\ or\ O(N^2)$)**

3. **더 빠른** 정렬이 필요한 문제
   - **계수 정렬($O(N + K)$)** 등 다른 알고리즘 또는 기존 알고리즘을 개선하여 풀이

<br>

### 1. 선택 정렬(Selection Sort)
> 가장 작은 데이터를 선택해 맨 앞의 데이터와 바꾸고, 그 다음 작은 데이터를 선택해 앞에서 두 번째 데이터와 바꾸는 방법

- 특정 리스트에서 가장 작은 데이터를 찾는 일이 잦으므로 익숙해질 필요가 있음
- 시간 복잡도 : $O(N^2)$
- 데이터 개수가 많아지면 비효율적인 방법임
  - 데이터 개수에 따른 수행 시간 : 선택 정렬 > 퀵 정렬 > 기본 정렬 라이브러리
- 예
    
    ![선택 정렬(Selection Sort)](/assets/images/posts/algorithm/this_is_coding_test/selection.png)

- 코드
    
    ```python
    array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]
    
    for i in range(len(array)): # 0 ~ 9
        min_index = i
        for j in range(i + 1, len(array)): # 1 ~ 9
            if array[min_index] > array[j]: # j끼리 비교하여 최솟값을 찾음
                min_index = j
        
        # 리스트 내 원소 위치 스와프(Swap)
        array[i], array[min_index] = array[min_index], array[i] 
    
    print(array)
    # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    ```

<br>

### 2. 삽입 정렬(Insertion Sort)
> 데이터를 하나씩 확인하며 각 데이터를 적절한 위치에 삽입하는 방법

- 데이터가 거의 정렬되어 있을 때 훨씬 효율적임
- 첫 번째 데이터는 정렬되어 있다고 생각하고, 두 번째 데이터 부터 정렬을 시작함
- 시간 복잡도
  - $O(N^2)$
  - 거의 정렬되어 있는 상태라면 퀵정렬 등 다른 알고리즘 보다 더 나을 수 있음 : $O(N)$
- 예
    
    ![삽입 정렬(Insertion Sort)](/assets/images/posts/algorithm/this_is_coding_test/insertion.png)

- 코드

    ```python
    array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]
    
    for i in range(1, len(array)): # 1 ~ 9
        for j in range(i, 0, -1): # i ~ 1, range(start, stop, step) : start부터 stop 인덱스 전까지 step만큼 증 또는 감소
            if array[j] < array[j - 1]: # j끼리 비교하여 원소 위치 스와프
                array[j], array[j - 1] = array[j - 1], array[j]
            else:
                break
    print(array)
    ```

<br>

### 3. 퀵 정렬(Quick Sort)
> 기준 데이터를 설정하고 그 기준보다 큰 데이터와 작은 데이터의 위치를 바꾸는 방법

- 정렬 알고리즘 중에 가장 많이 사용되는 알고리즘
- 병합 정렬과 퀵 정렬은 정렬 라이브러리에 근간이 되는 알고리즘
- 방법
    - 기준을 설정한 다음 큰 수와 작은 수를 교환한 후 리스트를 반으로 나누는 방식으로 동작
    - **피벗(=교환하기 위한 '기준') 사용**
    - 피벗을 설정하고 리스트를 분할하는 방법에 따라 여러가지 방식으로 퀵 정렬을 구분함
    - 대표적 분할 방식인 호어 분할(Hoare Partition) 방식 사용
        - 파트 1 : 피벗을 기준으로 분할하는 파트(= 분할 or 파티션)
            - **리스트에서 첫 번째 데이터를 피벗으로 설정**
            - 왼쪽에서 피벗보다 큰 데이터를, 오른쪽에서 피벗보다 작은 데이터를 찾고 서로를 바꿈
            - 바꾸다가 서로 값이 엇갈릴 경우 **작은 데이터와 피벗의 위치를 바꿈**
            - 피벗을 기준으로 왼쪽은 피벗보다 작고, 오른쪽은 피벗보다 큰 데이터가 위치하게 된다
        - 파트 2 : 피벗을 기준으로 왼쪽을 나눠서 정렬하는 파트
            - 파트 1과 같이 동일한 방법으로 진행
            - 리스트에 데이터가 1개만 남아 있으면 종료
        - 파트 3 : 피벗을 기준으로 오른쪽을 나눠서 정렬하는 파트
            - 파트 1과 같이 동일한 방법으로 진행
            - 리스트에 데이터가 1개만 남아 있으면 종료
- 예
    
    ![](/assets/images/posts/algorithm/this_is_coding_test/quick_sort.png)    

- 시간 복잡도
  - $O(NlogN)$
  - 이미 데이터가 정렬되어 있는 경우 : $O(N^2)$
- 코드

    ```python
    # 널리 사용되고 있는 직관적 형태의 퀵 정렬
    array = [5, 7, 9, 0, 3, 1, 6, 2, 4, 8]
    
    def quick_sort(array, start, end):
        if start >= end: # 원소가 1개인 경우 종료
            return

        pivot = start
        left = start + 1
        right = end

        while left <= right:
            # 피벗보다 큰 데이터 찾기
            while left <= end and array[left] <= array[pivot]:
                left += 1
            # 피벗보다 작은 데이터 찾기
            while right > start and array[right] >= array[pivot]:
                right -= 1

            if left > right: # 엇갈렸다면 작은 데이터와 피벗과 교체
                array[right], array[pivot] = array[pivot], array[right]
            else: # 엇갈리지 않았다면 작은 데이터와 큰 데이터를 교체
                array[left], array[right] = array[right], array[left]

        # 분할 이후 왼쪽부분과 오른쪽 부분에서 각각 정렬 수행(재귀함수 사용)
        quick_sort(array, start, right - 1)
        quick_sort(array, right + 1, end)
    
    quick_sort(array, 0, len(array) - 1)
    print(array)
    # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    ###################################################################  
  
    # 파이썬의 장점을 살린 퀵 정렬(시간 면에서 조금 비효율적이나 기억하기 쉬움)
    array = [5, 7, 9, 0, 3, 1, 6, 2, 4, 8]
    
    def quick_sort(array):
        if len(array) <= 1: # 원소가 1개인 경우 종료
            return array
    
        pivot = array[0]
        tail = array[1:] # 피벗을 제외한 리스트
    
        left_side = [x for x in tail if x<= pivot] # [0, 3, 1, 2, 4]
        right_side = [x for x in tail if x > pivot] # [7, 9, 6, 8]
    
        # 분할 이후 왼쪽, 오른쪽 정렬을 각각 수행하고 전체 리스트 반환
        return quick_sort(left_side) + [pivot] + quick_sort(right_side)
    
    print(quick_sort(array))
    # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
    ```

<br>

### 4. 계수 정렬(Count Sort)
> 특정 조건이 부합할 때만 사용할 수 있지만, 매우 빠른 정렬 알고리즘

- **특정 조건**
    - **데이터의 크기 범위가 제한($Max-Min ≤ 1,000,000$)되어 정수 형태로 표현할 수 있을 때(범위가 무한일 때 사용 불가)**
    - **유리한 상황**
        - 데이터 크기가 많이 중복되어 있을 때<br>
          (0, 0, 1, 2, 3, 4, 4, 4)
        - 정렬해야하는 데이터의 개수가 매우 많을 때
    - **불리한 상황**
        - 데이터 개수 대비 리스트 크기가 커야할 때<br>
          (0과 999,999 2개 데이터를 정렬 시, 필요한 리스트 크기 1,000,000)
- 방법
    - **가장 큰 데이터와 가장 작은 데이터의 범위가 모두 담길 수 있도록 하나의 리스트를 생성 후 0으로 초기화**
    - 데이터를 확인하며 데이터의 값과 동일한 인덱스의 데이터를 1씩 증가
    - 리스트의 첫 번째 데이터부터 하나씩 그 값만큼 인덱스를 출력
- 시간 복잡도 : O(N + K), 데이터 최댓값 : K
- 공간 복잡도 : O(N + K)
- 기수 정렬(Radix Sort)과 같이 가장 빠른 정렬 알고리즘
- 예

    <br>
    
    ![선택 정렬(Selection Sort)](/assets/images/posts/algorithm/this_is_coding_test/count.png)
    
    <br>

- 코드
    
    ```python
    array = [7, 5, 9, 0, 3, 1, 6, 2, 9, 1, 4, 8, 0, 5, 2]
    
    count = [0] * (max(array) + 1)
    
    for i in range(len(array)):
        count[array[i]] += 1 # 각 데이터의 인덱스 값에 해당하는 count 증가
    
    for i in range(len(count)):
        for j in range(count[i]):
            print(i, end= " ")
    ```

<br>

### 5. 파이썬의 정렬 라이브러리(내장 라이브러리)
> 이미 만들어진 라이브러리를 이용하여 정렬하는 방법

- 대부분 병합 정렬 or 퀵 정렬에 기반한 함수
- 시간 복잡도 : **O(NlogN)**
- 종류
    - list 정렬 방법
    
        ```python
        # 오름차순 정렬
        #   오름차순 정렬된 결과를 반환 후 변수에 저장하여 변수 출력
        array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]
        result = sorted(array)
        print(result) # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
        
        #   array 자체를 오름차순 정렬 후 array 출력
        array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]
        array.sort()
        print(array) # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
        
        # 내림차순 정렬
        array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]
        reverse = sorted(array, reverse=True)
        print(reverse) # [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
        
        # Array 뒤집기
        array = [7, 5, 9, 0, 3, 1, 6, 2, 4, 8]
        reverse = reversed(array)
        print(list(reverse)) # [8, 4, 2, 6, 1, 3, 0, 9, 5, 7]
        ```
    
    - Dictionary 정렬 방법
    
        ```python
        # Dictionary 자체 정렬
        #   Key 기준
        dict = {'A' : 1, 'D' : 3, 'C' : 4, 'B' : 2}
        kdict = sorted(dict.items()) # 내림차순일 경우 ,reverse = True 추가
        print(kdict) # [('A', 1), ('B', 2), ('C', 3), ('D', 4)]
        
        #   Value 기준
        dict = {'A' : 1, 'D' : 3, 'C' : 4, 'B' : 2}
        vdict = sorted(dict.items(), key = lambda x:x[1])
        print(vdict)
        
        # Key or Value만 정렬
        #   Key
        dict = {'A' : 1, 'D' : 3, 'C' : 4, 'B' : 2}
        kdict = sorted(dict.keys())
        print(kdict) # ['A', 'B', 'C', 'D']
        
        #   Value
        dict = {'A' : 1, 'D' : 3, 'C' : 4, 'B' : 2}
        vdict = sorted(dict.values())
        print(vdict) # [1, 2, 3, 4]
        ```

<br>

## 실전 문제

> 위에서 아래로

하나의 수열에는 다양한 수가 존재한다. 이러한 수는 크기에 상관없이 나열되어 있다. 이 수를 큰 수부터 작은 수의 순서로 정렬해야 한다.
수열을 내림차순으로 정렬하는 프로그램을 만드시오.

-> 가장 기본적인 정렬을 할 수 있는 지 물어보는 문제

- 시간 제한 : 1초
- 메모리 제한 : 128MB
- 입력 조건
  - 첫째 줄, 수열에 속해 있는 수의 개수 $1 <= N <= 500$
  - 둘째 줄부터 ~ N + 1 번째 줄까지, N개의 수가 입력됨($1 <= 입력된\ 수 <= 100,000$)

```python
# N 입력 받기
n = int(input())
# 3
# 15
# 27
# 12

# N개의 정수를 입력 받아 리스트에 저장
array = []
for i in range(n):
    array.append(int(input()))

# 파이썬 정렬 라이브러리를 이용하여 내림차순 정렬 수행
array = sorted(array, reverse=True)

# 정렬이 수행된 결과를 출력
for i in array:
    print(i, end=' ')
# 27 15 12
```

<br>

> 성적이 낮은 순서로 학생 출력하기

N명의 학생의 성적 정보가 있다. 학생 정보는 학생의 이름과 학생의 성적으로 구분된다. 각 학생의 이름과 성적 정보가 주어졌을 때 성적이 낮은 순서대로 학생의 이름을 출력하는 프로그램을 작성하시오.

- 시간 제한 : 1초
- 메모리 제한 : 128MB
- 입력 조건
    - 첫 번째 줄에 학생의 수 N이 입력된다. (1 <= N <= 100,000)
    - 두 번째 줄 부터 N+1 번째 줄에는 학생의 이름을 나타내는 문자열 A와 학생의 성적을 나타내는 정수 B가 공백으로 구분되어 입력된다. 문자열 A의 길이와 학생의 성적은 100 이하의 자연수이다.

-> 튜플로 저장 후 인덱스 1에 위치한 값을 기준으로 정렬하고, 인덱스 0의 값을 순서대로 출력하면 된다.

```python
n = int(input())
# 2
# 홍길동 95
# 이순신 77

array = []
for i in range(n):
    input_data = input().split()
    array.append((input_data[0], int(input_data[1])))

array = sorted(array, key=lambda student: student[1])

for student in array:
    print(student[0], end=' ')
# 이순신, 홍길동
```

<br>

> 두 배열의 원소 교체

동빈이는 두 개의 배열 A와 B를 가지고 있다. 두 배열은 N개의 원소로 구성되어 있으며, 배열의 원소는 모두 자연수이다. 
동빈이는 최대 K 번의 바꿔치기 연산을 수행할 수 있는데, 바꿔치기 연산이란 배열 A에 있는 원소 하나와 배열 B에 있는 원소 하나를 골라서 두 원소를 서로 바꾸는 것을 말한다. 
동빈이의 최종 목표는 배열 A의 모든 원소의 합이 최대가 되도록 하는 것이며, 여러분은 동빈이를 도와야한다.

N, K, 그리고 배열 A와 B의 정보가 주어졌을 때, 최대 K 번의 바꿔치기 연산을 수행하여 만들 수 있는 배열 A의 모든 원소의 합의 최댓값을 출력하는 프로그램을 작성하시오.

예를 들어 N = 5, K = 3이고, 배열 A와 B가 다음과 같다고 하자.

- 배열 A = [1, 2, 5, 4, 3]
- 배열 B = [5, 5, 6, 6, 5]

이 경우, 다음과 같이 세 번의 연산을 수행할 수 있다.

- 연산 1) 배열 A의 원소 '1'과 배열 B의 원소 '6'을 바꾸기
- 연산 2) 배열 A의 원소 '2'와 배열 B의 원소 '6'을 바꾸기
- 연산 3) 배열 A의 원소 '3'과 배열 B의 원소 '5'를 바꾸기

세 번의 연산 이후 배열 A와 배열 B의 상태는 다음과 같이 구성될 것이다.

- 배열 A = [6, 6, 5, 4, 5]
- 배열 B = [3, 5, 1, 2, 5]

이때 배열 A의 모든 원소의 합은 26이 되며, 이보다 더 합을 크게 만들 수는 없다. 따라서 이 예시의 정답은 26이 된다.

- 시간제한 : 2초
- 메모리제한 : 128MB
- 입력 조건
    - 첫 번째 줄: N, K 가 공백으로 구분되어 입력 (1 <= N <= 100,000, 0 <= K <= N)
    - 두 번째 줄: 배열 A의 원소들이 공백으로 구분되어 입력 (원소 a < 10,000,000인 자연수)
    - 세 번째 줄: 배열 B의 원소들이 공백으로 구분되어 입력 (원소 b < 10,000,000인 자연수)
    
-> 기본 정렬 알고리즘과 두 원소를 교체하는 방법만 알고 있으면 쉽게 푸는 문제  

```python
n, k = map(int, input().split()) # N과 K를 입력 받기
a = list(map(int, input().split())) # 배열 A의 모든 원소를 입력받기
b = list(map(int, input().split())) # 배열 B의 모든 원소를 입력받기

a.sort() # 배열 A는 오름차순 정렬 수행
b.sort(reverse=True) # 배열 B는 내림차순 정렬 수행

# 첫 번째 인덱스부터 확인하며, 두 배열의 원소를 최대 K번 비교
for i in range(k):
    # A의 원소가 B의 원소보다 작은 경우
    if a[i] < b[i]:
        # 두 원소를 교체
        a[i], b[i] = b[i], a[i]
    else: # A의 원소가 B의 원소보다 크거나 같을 때, 반복문을 탈출
        break

print(sum(a)) # 배열 A의 모든 원소의 합을 출력
```
