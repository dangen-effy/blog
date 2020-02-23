---
title: "Janus 소개와 Docker 기반으로 쉽게 사용하기"
date: 2020-02-23T15:22:11+09:00
draft: false
---

[리모트몬스터](https://www.remotemonster.com/)의 백엔드 로직 중 일부는 Janus의 힘을 빌리고 있습니다. 이번 시간에는 Janus가 무엇인지 알아보고 설치와 데모 실행까지 다뤄보겠습니다.

## Janus가 뭘까?

[Janus](https://janus.conf.meetecho.com/)는 RTC 서비스 기술지원 및 컨설팅 전문회사 [Meetcho](https://www.meetecho.com/en/)에서 만든 범용 WebRTC 서버입니다. 범용 서버인 만큼, 브라우저와 WebRTC 미디어 통신을 설정하고, 브라우저와 JSON 메시지를 교환하며, 브라우저와 연결된 서버측 애플리케이션 로직 간에 RTP/RTCP와 메시지를 전달하는 방법 외에는 어떠한 기능도 제공하지 않습니다.

이 말인즉슨, 서버 사이드에서 특정 기능 및 애플리케이션을 구현해야합니다. 이를 Plugin이라고 부르며 예로는 Echo Test, Video Call 그리고 Audio Bridge 등의 애플리케이션을 구현 할 수 있습니다.

## 직접 구현해야하면 왜 사용하는거죠?

Janus는 기본적으로 다수의 강력한 기본 플러그인을 제공하고 있습니다.

* Echo Test

* Streaming

* Video Call

* SIP Gateway

* Video Room

* Audio Room 외 4개

직접 구현해야 한다는 말은, 만약 기본 플러그인 외에 특수한 기능이 필요하다면 직접 구현해 사용 가능하다는 맥락입니다. 이 자체를 장점으로 볼 수 있겠죠?

## 단점

1. 의존성

Janus의 모든 기능을 포함해 빌드하기 위해서 20+ 가지의 [라이브러리](https://github.com/meetecho/janus-gateway/#dependencies)를 설치해야합니다. 이 때문에 서비스를 시작하기까지 드는 비용이 큽니다. 초기 빌드 시간이 오래 걸리며, Containerize시 이미지의 사이즈가 상대적으로 무거운점 등 몇 가지 제약이 생깁니다.

하지만 이를 극복할 방법은 있습니다. 여러분이 Janus에서 사용할 기능에 따라서 빌드 전에 필수로 설치해야하는 라이브러리가 달라집니다. 예를 들어 WebSocket이 필요 없다면 libwebsockets 은 설치할 필요가 없습니다.

2. 문서 (국내)

한글로 작성된 정보들이 부족합니다.

![](https://cdn-images-1.medium.com/max/2000/0*5dBZYk0_XxUj69cz.png)

지금부터 Janus의 도커 이미지를 만들고 빌드 및 실행, 마지막으로 Video Room 데모 시연까지 진행하겠습니다. 자세한 빌드 스텝이 궁금하신 분은 [janus-gateway](https://github.com/meetecho/janus-gateway) 깃헙 문서를 참조해주세요.

## 전제 조건

* Docker

* Chrome browser

먼저 소스코드를 받아주세요.

    $ git clone [https://github.com/dangen-effy/docker-janus-webrtc-gateway](https://github.com/dangen-effy/docker-janus-webrtc-gateway)

[docker-janus-webrtc-gateway](https://github.com/dangen-effy/docker-janus-webrtc-gateway) 저장소는 번거로운 Janus 빌드를 손쉽게 Docker 기반으로 진행 할 수 있는 프로젝트입니다.

Janus가 요구하는 라이브러리들을 설치할때 OS 종속적인 명령어들 때문에 골치 아픈 경우가 있습니다. (레드햇 계열의 yum, 데비안 계열의 apt-get) 심지어 OS에 따라 패키지명만 조금씩 다른 케이스도 있습니다.(libconfig-dlevel, libconfig-dev, libconfig)

하지만 해당 프로젝트의 Dockerfile 은 Ubuntu 16 위에서 Janus가 잘 작동하도록 모든 빌드 스텝을 정의해두었기 때문에 걱정하지 마시고 사용하시면 됩니다. 😀

이제 도커 이미지 빌드를 해주세요.

    $ docker build -t janus .

    Step 1/15 : FROM ubuntu:16.04
    16.04: Pulling from library/ubuntu
    f7277927d38a: Pull complete 
    8d3eac894db4: Pull complete 
    Digest: sha256:97b54e5692c27072234ff958a7442dde4266af21e7b688e7fca5dc5acc8ed7d9
    Status: Downloaded newer image for ubuntu:16.04
     ---> 5e13f8dd4c1a
    ...

컨테이너를 실행해주세요.

    $ docker run -d -p 80:80 -p 8088:8088 -p 8188:8188 janus

다음은 포트가 담당하는 서비스입니다.

* 80: Web server

* 8088: RESTful API

* 8188: Websocket API

Janus 서버가 잘 실행됐는지 확인합니다.

[http://127.0.0.1](http://127.0.0.1) 에 접속해주세요.

![](https://cdn-images-1.medium.com/max/4224/1*rlRzDTFHSGMwZIXxNA0rJg.png)

👏 축하합니다. 성공적으로 Janus를 가동시켰습니다.

이제 [Demos] — [Video Room] 메뉴로 이동합니다. 브라우저 탭을 여러개 열어 화상 채팅에 참여해보세요.

![haha1, haha2, haha3 유저 세명이서 화상 채팅을 하는 모습입니다.](https://cdn-images-1.medium.com/max/4140/1*uPz0kTJn1l1zhNqVtB8zlw.png)*haha1, haha2, haha3 유저 세명이서 화상 채팅을 하는 모습입니다.*

## 정리

Janus를 이용하면 이처럼 개발 비용을 대폭 줄이고 WebRTC 기능을 빌려 컨퍼런스 콜 및 여러 플러그인들을 사용할수 있습니다. 이를 기반으로 사내 화상 회의 앱 부터 다양한 서비스를 만들수 있겠죠?

다음 포스팅은 Janus의 아키텍처와 작동 방식에 대해서 작성할 예정입니다. 감사합니다.
