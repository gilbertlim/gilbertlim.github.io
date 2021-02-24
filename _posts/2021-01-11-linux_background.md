---
title: "[Linux] 백그라운드(Background) 실행"
category: Linux
---

# Background 실행

### 백그라운드 프로그램 조회

- `jobs` : 현재 백그라운드에서 실행중인 작업 표시

### 백그라운드 프로그램으로 전환

- `Ctrl` + `z` : background 실행 전환

### 포그라운드 프로그램으로 전환

- ` fg`  : foreground 실행 전환
    - `+` : `fg` 시 나타나는 프로그램(1번째 대기)
    - `-` : `+` 다음 나타나는 프로그램(2번째 대기)
    - `fg %N` : 백그라운드 프로그램 중 실행시키고 싶은 프로그램 실행

### 프로그램 삭제

- ` kill` 

     : 프로그램 종료

    - `kill %N` : 종료시키고 싶은 프로그램 종료
    - `kill -9 %4` : 강력한 종료

### 처음 실행할 때부터 백그라운드 프로그램으로 실행

` [COMMAND] &` 
