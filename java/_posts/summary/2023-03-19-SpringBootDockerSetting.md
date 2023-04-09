---
layout: post  
title:  "SpringBoot Database Error."
description:  
date: 2023-03-19 14:00 PM  
categories: summary
---

### SpringBoot Docker 개발환경 세팅

#### 확인을 위한 기본적인 api 세팅


```java
@RestController
@RequestMapping("/")
public class HomeController {
    @GetMapping()
    public ResponseEntity<String> home() {
        return ResponseEntity.ok("TEST!");
    }
}
```

#### Java Build

```shell
./gradlew build -x test
```


Docker에서 SpringBoot가 실행될 환경을 만들기 위해 Dockerfile을 작성해야합니다

#### Dockerfile 이란?
* docker에서 이용하는 이미지를 기반으로 커스터 마이징 한 퍼스널 이미지를 생성할수 있도록 하는 이미지 설정파일 입니다.

[DockerFile 이란?]: /server/summary/2023-04-09-DockerFileCompose/

```dockerfile
FROM openjdk:11
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java","-jar","-Dspring.profiles.active=dev","/app.jar"]
```

#### Docker Image 생성

위에서 작성한 Dockerfile 을 이용하여 이미지를 생성합니다.

```shell
# gradle
docker build --build-arg DEPENDENCY=build/dependency -t DockerHub ID/Repository --platform linux/amd64 .

# maven
docker build -t DockerHub ID/Repository --platform linux/amd64 .

# Spring Boot 2.3 버전 이상인 경우 Dockerfile 없이 이미지 생성
gradlew bootBuildImage --imageName=foo/bar

# 멀티 프로젝트일 경우 특정 프로젝트의 이미지를 빌드
gradlew {project_name}:bootBuildImage --imageName=foo/bar
```


#### Docker Image 배포

```shell
# 업로드
docker push ID/Repository

# 업로드한 이미지 배포
docker pull ID/Repository
```

