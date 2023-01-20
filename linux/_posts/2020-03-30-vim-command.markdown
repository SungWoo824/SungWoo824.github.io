---
layout: post
title:  "vi,vim 명령어"
description: vi, vim 정의 및 명령어 정리.
date:   2020-03-30 +0530
categories: Linux vim
---
Linux, Unix 환경에서는 터미널에서 vi, vim 에디터를 기본으로 사용한다. 코딩을 시작하기 전까지는 윈도우환경에서만 컴퓨터를 사용했었던 터라 이 부분은 자꾸 헷갈리게 되어 다시한번 정리하며 포스팅을 하게 되었다.



## 1. 파일 실행
```bash
$ vim [file]
```
 파일을 vim으로 실행시킨다.

<br/>

## 2. 입력 모드 전환
    
| 명령어 | 동작 |
    :------: | :---------:
    `i` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;커서가 있는 위치에서 입력 모드 전환
    `R` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;수정 모드로 전환
    `o` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;커서가 위치한 줄 아래 빈 줄 삽입
    `O` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;커서가 위치한 줄 위에 빈 줄 삽입

<br/><br/>


## 3. 커서 이동

| 명령어 | 동작 |
:------: | :---------:
`H` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;화면 맨 위로 이동
`M` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;화면 중간으로 이동
`L` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;화면 맨 아래로 이동
`G` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;글의 맨 밑으로 이동
`1G` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;글의 맨 처음으로 이동

<br/>
Mac 에서 터미널을 사용할때 키보드방향키나 마우스 휠로도 이동은 가능하다.

<br/>

## 4. 삭제

| 명령어 | 동작 |
:------: | :---------:
`x` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;한 문자 삭제
`dd` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;한 줄 삭제
`dw` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;한 단어 삭제
`u` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;실행 취소

<br/>
## 5. 저장 및 종료

| 명령어 | 동작 |
:------: | :---------:
`:w` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;저장하기
`:wq` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;저장 후 종료
`:q` | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;저장하지 않고 종료

<br/>

리눅스 환경에서 개발을 하다보면 터미널에서 텍스트에디터로 자주 사용하게 되므로 기본적인 삽입, 삭제 저장 등의 명령어는 익숙해질 필요가 있다고 느꼈다.

<br/>
