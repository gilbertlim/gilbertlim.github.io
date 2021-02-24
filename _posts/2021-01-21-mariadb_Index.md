---
title: "[MariaDB] 인덱스(Index)"
category: MariaDB
---

# 9. 인덱스

## 9.1 인덱스의 개념

> 색인, 조회할 때 원하는 행을 빠르게 찾을 수 있게 준비해둔 데이터
>
> 대용량의 테이블일 경우 효과적인 데이터를 좀 더 빠르게 찾을 수 있도록 해주는 도구

#### 장단점

장점

- 대용량 테이블에서 검색 속도 증가(아닌 경우도 존재함)
- 해당 쿼리의 부하 감소 $\rightarrow$ 시스템 전체 성능 향상

단점

- 인덱스가 DB 공간을 차지(DB 크기의 10%)
- 처음 인덱스를 생성하는데 시간이 많이 소요됨
- 데이터 변경 작업(INSERT, UPDATE, DELETE)이 자주 일어날 경우, 성능 하락

## 9.2 인덱스의 종류와 자동 생성

### 9.2.1 인덱스의 종류

#### 클러스터형 인덱스(Clustered Index)

- 테이블 당 한 개만 생성
- 행 데이터를 인덱스로 지정한 열에 맞춰 자동 정렬
- 보조 인덱스보다 검색 속도는 빠르지만, 데이터의 입력/수정/삭제는 더 느림
- 예 : 영어사전

#### 보조 인덱스(Secondary Index)

- 테이블당 여러 개 생성 가능
- 클러스터형 인덱스보다 검색 속도는 느리지만, 데이터의 입력/수정/삭제는 덜 느림
- 예 : 책 뒤에 '찾아보기'가 있는 책

### 9.2.2 자동으로 생성되는 인덱스

>  인덱스는 필드 단위에 생성(필드 수만큼 인덱스 설정 가능)

- 클러스터형 인덱스 자동 생성
    - PK
    - UNIQUE NOT NULL
    - PK + Unique NOT NULL $\rightarrow$ PK 필드에 인덱스 생성
- 보조 인덱스 자동 생성
    - UNIQUE
    - UNIQUE NULL
- 테이블 생성 시 Unique, Unique NULL로 설정하면 보조 인덱스 생성

### 9.2.3 인덱스 생성/변경/삭제

- 인덱스 생성 : `CREATE INDEX [인덱스명] ON [테이블명] ([필드명]);`
- 인덱스 변경 : `ALTER TABLE [테이블명] ADD INDEX [인덱스명] ([필드명]);` 
- 인덱스 삭제 : `DROP INDX [인덱스명] ON [테이블명];`

## 9.3 결론

#### 결론

- **인덱스는 데이터를 읽을 때만 성능을 향상 시킴**
- 인덱스는 열 단위에 생성(두 개이상의 열을 하여 생성 가능)
- 외래키로 지정한 필드는 자동으로 외래키 인덱스 생성
- 클러스터형 인덱스는 테이블당 1개씩만 생성 가능

#### 인덱스를 생성해야하는 경우

- WHERE절에서 사용되는 열에 인덱스를 생성해야 함(WHERE절에 사용되어도, 자주 사용해야 의미가 있음)
- JOIN이 자주 사용되는 필드는 인덱스를 생성하는 것이 좋음

#### 인덱스를 생성하지 말아야하는 경우

- 데이터 중복도가 높은 열은 인덱스의 효과가 없음
- INSERT/UPDATE/DELETE 시 검색속도가 저하되므로 빈도수를 고려하여 생성 필요
