---
layout: post  
title:  DockerFile 작성
description:  
date: 2023-04-09 13:30 PM  
categories: summary
---

## DockerFile 이란

Docker Hub 에 있는 이미지만을 사용 하는것이 아닌 개발 환경에 맞는 설정을 주기위해 작성하는
파일이 DockerFile 입니다.

컨테이너에 설치하는 패키지, 코드, 실행하는 명령어, 환경변수 설정 등을 DockerFile 에 정의하면
이를 기반으로 이미지를 생성할수 있습니다.


### DockerFile 형식

```dockerfile
# this line is removed before run dockerfile 
RUN echo 'hello' \
    # this line is removed before run dockerfile
    RUN echo 'world'
RUN echo "\
     hello\
     world"
```

명령어는 대소문자를 구분하지 않지만, 인수와 쉽게 구분하기위해 관례적으로 대문자를 사용합니다.
주석은 DockerFile 이 실행될때 제거되고 동작합니다.
주석과 명령어 앞의 공백은 무시되지만, 인수에 포함되어 있는 공백은 유지됩니다.

#### Parser directives

구문 지시자는 다음 라인의 명령들이 어떻게 적용될지 설정해줍니다.
대소문자를 구분하지 않지만 관례적으로 소문자만을 이용합니다. 다음에 빈줄 하나를 넣는것 또한 암묵적인 규칙입니다.

구문 지시자는 빌드에 레이어를 추가하지 않으며 빌드 단계로 표시되지 않습니다. 구문 분석기 지시문은 형식의 특수 유형의 주석으로 작성됩니다.
빈줄 이나 빌드 명령어가 실행 한 이후에는 더이상 구문 지시자를 찾지 않고 모든 형태를 주석으로 처리하기 때문에
Dockerfile 의 최 상단에 위치해야합니다.

```dockerfile
# excape=`
```
```dockerfile
# excape=\
```

#### Environment replacement

환경변수는 선언이후 `$variable_name` 또는 `${variable_name}` 의 형식으로 사용할수 있습니다.

몇몇의 bash 연산자를 지원합니다.
 * `${variable:-word}` - `variable` 이 정의되어 있지 않다면 `word` 로 대체됩니다.
 * `${variable:+word}` - `variable` 이 정의되어 있지 있다면 `word` 로 대체되고 없다면 빈 문자열로 대체됩니다.

```dockerfile
ENV foo /bar
WORKDIR ${foo}
```

#### FROM

새로운 빌드 단계를 준비하고 다음 멸령어들의 기본 이미지를 지정합니다.

```dockerfile
FROM [--platform=<platform>] <image> [AS <name>]

FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]

FROM [--platform=<platform>] <image>[@<digest>] [AS <name>]
```