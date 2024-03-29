---
title: "[Practical Statistics] 상관관계 분석(Correlation Analysis)"
category: Practical Statistics
use_math: true
---

# Correlation Analysis(상관관계 분석)

## 1. 상관관계 vs 인과관계
### 1) 상관관계
> 원인과 결과가 불분명한 관계

- 방향성이 없음

### 2) 인과관계
> 선행하는 변수의 변화가 다른 변수의 원인으로 작용하는 관계

- 방향성이 있음

<br>

## 2. 상관관계 분석(Correlation)
> 두 변수간에 어떤 관계를 갖고 있는지를 분석하는 방법

- 연속형(수치형) 데이터에 적용
- 일반적으로 표본의 상관계수를 구함
- 양의 상관관계 또는 음의 상관관계로 나타남

### 공분산
> 두 변수가 함께 변화하는 정도를 나타낸 지표(공통적으로 얼마나 떨어져 있는지)

- 공분산 부호
    - `+` : 두 변수가 같은 방향으로 변화
    - `-` : 두 변수가 반대 방향으로 변화
- 공분산 크기
    - `0` : 두 변수는 독립
    - 공분산의 크기가 클수록 두 변수는 함께 많이 변화한 것
- 단위에 따라 크기가 달라지므로, 절대적 크기로 판단이 어려움
    - **공분산을 -1 ~ 1 범위로 표준화**하여 사용 -> **상관계수**

### 상관계수
> 관계가 있는 경우, 얼마나 관계가 있는지 나타내는 수치 <br>
> 공분산을 두 변수의 표준편차의 곱으로 나눈 값

$r = \cfrac{\sum(x-\bar{x})(y-\bar{y}}{\sqrt{\sum(x-\bar{x})^2 \sum(y-\bar{y})^2}}$

$-1 \le r \le 1$

|상관계수|상관관계|
|---|---|
|$\pm$ 0.9 이상|상관관계가 아주 높다|
|$\pm$ 0.7 ~ 0.9|상관관계가 높다|
|$\pm$ 0.4 ~ 0.7|상관관계가 있다|
|$\pm$ 0.2 ~ 0.4|상관관계가 있으나 낮다|
|$\pm$ 0.2 미만|상관관계가 거의 없다|
