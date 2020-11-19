---
title: "가장 간단한 Dockerfile 작성 방법 🐋"
description: "도커파일의 명령어들을 알아봅시다"
tags: ["Docker", "Dockerfile"]
categories: ["Docker", "Dockerfile"]
date: 2020-11-19T21:00:00+09:00
draft: true
---

<img src="/images/docker_basic.png" alt="Docker" width="250">

<br/>

도커를 이용하면 애플리케이션을 언제나 동일한 환경에서 구동할수 있습니다. 컨테이너가 어떤 환경 위에서 실행될지 선언해주고 (예: CentOS, Ubuntu, ...) 그 위에 우리가 작성한 소스코드, 즉 애플리케이션을 구동하면 되니까요.

# Dockerfile 어떻게 작성할까?

도커파일은 명령어(Instruction)들의 조합으로 이루어집니다. 가장 흔하게 사용하는 명령어들부터 알아볼까요?

1. FROM

우리가 빌드할 도커 이미지가 어떤 이미지 위에서 실행될지 알려주는 명령어입니다. CentOS 위에서 실행한다고 가정해볼까요?

```dockerfile
# Dockerfile 입니다
FROM centos
```

위 내용을 `Dockerfile` 파일에 작성해보세요. 그리고 해당 폴더에 접근한 뒤 아래 명령어로 이미지를 빌드해봅시다!

```
➜  docker build -t kiwi .
Sending build context to Docker daemon  3.072kB
Step 1/1 : FROM centos
 ---> 0d120b6ccaa8
Successfully built 0d120b6ccaa8
Successfully tagged kiwi:latest
```

- `-t` 옵션은 이미지를 식별하기 위해서 태그를 붙여주는 옵션입니다. 저는 `kiwi` 라는 태그를 부여해줄게요.

도커 이미지를 빌드하면 여러분의 로컬 머신의 디스크 어딘가에 저장됩니다. 한 번 확인해볼까요?

```
➜  docker images
REPOSITORY  TAG     IMAGE           ID  CREATED     SIZE
kiwi        latest  0d120b6ccaa8    3   months ago  215MB # <- kiwi 이미지가 존재합니다!
```

이제 컨테이너를 실행(Run)해봅시다.

**갑분싸 주의**

```
➜  docker run kiwi
```


실행했는데 아무런 출력도 안나오고 이게 뭐하는건가 싶죠? 이유는 간단합니다. 우리가 작성한 도커파일에서 아무것도 하지 말라고 했기 때문입니다.  (위에서 작성한 `Dockerfile` 을 다시 한번 읽어보세요)


## 컨테이너에게 일 시키기

지금까지 만든 도커 이미지는 실행시켜도 아무런 일을 하지 않아 쓸모가 없습니다. 최소한  `Hello World` 라도 출력하게 만들어봅시다.

그런데 문자열 출력은 어떻게 할까요? 쉘 명령어 중에서 `echo` 를 사용하면 됩니다. 한 번 터미널을 열어 직접 사용해볼까요?

```
➜  echo Hello World
Hello World # <- 출력을 잘 하는 모습
```

문자열을 터미널에 출력하는 방법을 알아냈으니 해당 명령어(Instruction)를 도커파일에 입력해봅시다.

2. RUN

우리는 도커 이미지를 실행(Run)할때 `Hello World` 를 출력하길 원합니다. 이때 사용하는 명령어가 `RUN` 입니다. 아래처럼 도커파일을 수정해주세요.

```dockerfile
FROM centos

RUN echo Hello World # <- 여깁니다. 컨테이너를 실행할때 읽는 명령어입니다.
```

이제 빌드하고 실행해볼까요?

```
➜  docker build -t kiwi .
Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM centos
 ---> 0d120b6ccaa8
Step 2/2 : CMD echo Hello World # <- 우리가 방금 추가한 명령어(Instruction)
 ---> Running in b088437f0928
Removing intermediate container b088437f0928
 ---> 178a229a97f3
Successfully built 178a229a97f3
Successfully tagged kiwi:latest

➜  docker run kiwi
Hello World # <- 잘 출력하는 모습
```
