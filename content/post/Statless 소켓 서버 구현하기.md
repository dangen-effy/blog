---
title: "Statless 소켓 서버 구현하기"
date: 2020-02-23T15:25:14+09:00
draft: false
---

Photo by Troy Bridges on Unsplash

애플리케이션을 무 상태로 구현하는 것은 매우 중요합니다. 그래야 언제든 확장 및 축소가 가능하기 때문이죠. 그러나 구조적으로 유 상태 애플리케이션이 되는 것들이 있습니다. 대표적인 사례가 보통 소켓 통신을 사용하는 WebRTC의 시그널링 서버입니다. 따라서 이를 무 상태 구조로 만들어 주는게 가장 큰 과제죠.

소켓 서버가 유 상태인 이유는 보통 소켓은 사용자끼리 데이터를 주소 받을때 사용합니다. 즉 사용자A와 사용자B가 채팅 방에 접속해 대화를 나누는 시나리오가 이미 유 상태라는거죠. 이유는 간단합니다. 소켓 서버에 접속한 클라이언트 정보가 서버 프로세스의 메모리 영역에서 관리되고 있기 때문입니다.

만약 사용자B가 실수로 채팅을 나가서 재 접속을 한다고 칩시다. 이때 로드밸런서가 최초 사용자A와 연결을 맺어준 서버로 밸런싱을 해준다는 보장이 없습니다. 따라서 재 접속시 언제나 사용자A와 통신 가능하도록 만들어줘야하죠.

방법은 두가지입니다.

* 스티키 세션 (L4 네트워크 영역)

* 백엔드 서비스에 저장 (L7 애플리케이션 영역)

**스티키 세션**은 로드밸런서가 기존에 요청했던 정보를 기억해 다음 요청시 같은 서버로 밸런싱 해주는 기능입니다. 스티키 세션을 이용하면 애플리케이션의 코드 수정 없이 무 상태 애플리케이션을 구현 할 수 있는 장점이 있습니다.

하지만 스티키 세션이 맺어진 서버가 닫히면 클라이언트의 요청은 실패합니다. 또한 요청을 균등하게 분배해야 하는 로드밸런서의 기능도 퇴화됩니다. 따라서 이는 가용성을 감소시키는 원인이 될 수 있습니다.

자세한 내용은 [Pros and Cons of Sticky Session / Session Affinity load blancing strategy?](https://stackoverflow.com/questions/1553645/pros-and-cons-of-sticky-session-session-affinity-load-blancing-strategy)를 참조해주세요.

**백엔드 서비스**에 저장하는 일은 Redis 같은 곳에 소켓의 상태를 저장합니다. 덕분에 각 서버 프로세스 메모리에 의존하는 일이 없어져 손 쉽게 무 상태로 소켓 서버를 만들어 줄 수 있습니다.

그러나 애플리케이션이 의존하는 백엔드 서비스가 생기다 보니 Redis 같은 데이터 저장소의 가용성을 확보해야 하는 책임이 생깁니다.

### 관련 글

* [Pros and Cons of Sticky Session / Session Affinity load blancing strategy?](https://stackoverflow.com/questions/1553645/pros-and-cons-of-sticky-session-session-affinity-load-blancing-strategy)
