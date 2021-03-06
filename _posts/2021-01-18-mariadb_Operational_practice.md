---
title: "[MariaDB] MariaDB 운영 실습"
category: MariaDB
---

# 3. MariaDB 전체 운영 실습

##  3.1 요구사항 분석과 시스템 설계 그리고 모델링

### 3.1.1 정보시스템 구축 절차 요약

#### 구축 5단계

1. 분석 : 시스템 분석 및 요구사항 분석 
2. 설계 : 시스템 설계 및 프로그램 설계
3. 구현 : 설계서 기반 프로그램 작성
4. 시험 : 구현된 시스템의 정상 작동여부, 요건과 부합하는지 테스트
5. 유지보수 : 배포 및 운영, 유지보수

### 3.1.2 DB 모델링과 필수 용어

#### DB 모델링

- 현실 세계의 데이터를 DB에 어떻게 옮길 것인지 결정하는 과정
- 저장할 정보는 테이블 형식에 맞춰 저장

#### DB 모델링 관련 용어

- 정보의 진화 단계
    1. Data : Raw data(정보는 있으나 체계화되지 못한 상태)

    2. Information : 가공된 Data

    3. Knowledge : Information 이용에 대한 노하우

    4. Wizdom : Knowledge을 활용하는 창의적인 아이디어

- 테이블
    - 데이터를 입력하기 위해 표 형태로 표현한 것

- DB

    - 테이블이 저장되는 저장소
    - 고유한 이름을 가져야 함

- DBMS

    - DB를 관리하는 시스템 또는 소프트웨어

- 열(Column, Field)

    - 테이블의 구성 요소

    - 각 열을 구분하기 위한 이름은 고유해야 함

- 데이터 형식

    - 열의 데이터 형식(INT, VARCHAR, DATE, ...)

- 행(Row, Record)
    - 실질적인 데이터

- 기본키(Primary Key)

    - 각 행을 구분하는 유일한 열
    - 중복과 NULL 금지
    - 각 테이블에 하나만 지정

- 외래키(Foreign Key)

    - 두 테이블의 관계를 맺어주는 키

- SQL

    - 사람과 DBMS가 소통하기 위한 언어

## 3.2 MariaDB를 이용한 DB 구축 절차

> DB 생성 > 테이블 생성 > 데이터 입력 > 데이터 조회/활용

### 3.2.1 DB 생성

- `CREATE DATABASE [DB명]` : DB 생성
- `DROP DATABASE [DB명]` : DB 삭제
- `SHOW DATABASES` : DB 조회
- `SELECT DATABASE()` : 현재 선택된 DB 확인
- `USE [DB명]` : DB 선택

### 3.2.2 테이블 생성

- ```mariadb
  CREATE TABLE [테이블명](
    	[컬럼명] [데이터 형식] [NULL 허용] [옵션],
    ...,
      PRIMARY KEY([컬럼명])
  )) ENGINE=[엔진명] DEFAULT CHARSET=utf8;  
  ```
  
- `SHOW TABLES` : 테이블 조회

- `DESC [테이블명]` : 필드 조회

- `DROP TABLE [테이블명]` : 테이블 삭제

- DB에서는 테이블, 열 이름을 모두 소문자로 처리함

###  3.2.3 데이터 입력

- ```mariadb
INSERT INTO [테이블명] ([컬럼명], ...)
	VALUES ([값], ...), ([값], ...), ...;
	```

### 3.2.4 데이터 활용

- `SELECT * FROM [테이블명]` : 해당 테이블의 모든 데이터 조회

- `` `공 백` `` : 공백 처리 방법
- `-- [SQL문]` : 주석처리 방법

## 3.3 테이블 외의 DB 개체의 활용

### 3.3.1 인덱스

- 책 뒤에 붙어 있는 색인과 같은 개념
- 대용량 데이터의 경우 필요
- DB의 기능을 향상시키는 튜닝에서 중요한 요소(쿼리문에 대한 응답속도 향상)
- 데이터의 컬럼 단위에 생성

- 인덱스가 없을 경우 테이블 전체를 검색하고, 인덱스를 활용할 경우 trigger 단어를 찬아본 후 검색하므로 속도가 빠름

### 3.3.2 뷰

- 실제 행 데이터를 가지고 있지 않은 가상의 테이블
- 사용자 입장에서는 테이블과 동일하게 보임
- 실체는 없고, 테이블에 링크된 개념
- 뷰를 조회하면 실제 테이블의 데이터를 조회하는 것과 동일한 결과 출력
- 데이터 보호 및 보안을 위해 사용


### 3.3.3 스토어드 프로시저

- MariaDB에서 제공하는 프로그래밍 기능
- SQL문을 하나로 묶어 편리하게 사용하는 기능
- SQL문을 하나씩 실행하는 것 보다 스토어드 프로시저로 관리하는 경우가 많음

### 3.3.4 트리거

- 테이블에 부착되어서 테이블에 INSERT, UPDATE, DELETE 작업이 발생되면 실행되는 코드
- 예 : 회원이 탈퇴할 경우 탈퇴자 테이블에 데이터를 일부 옮겨놓고 지우는 작업

## 3.4 DB 백업 및 관리

### 3.4.1 백업과 복원

#### 백업

- 현재 DB를 **다른 매체**에 보관하는 작업
- 다른 디스크에 백업을 받아야 함

#### 복원

- DB에 문제가 생겼을 경우, 다른 매체에 백업된 데이터를 이용하여 원래 상태로 돌려 놓는 작업