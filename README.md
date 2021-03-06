- [개요](#개요)
- [1. 플레이어에 필요한 기능 구현](#1-플레이어에-필요한-기능-구현)
- [2. 새로고침 시 autoPlay](#2-새로고침-시-autoplay)
  - [2.1. 크롬 정책](#21-크롬-정책)
    - [2.1.1. Media Engagement Index (MEI)](#211-media-engagement-index-mei)
    - [2.1.2. 예시 시나리오](#212-예시-시나리오)
- [2. hls로 영상 재생하기](#2-hls로-영상-재생하기)
  - [2.1. 용어 정리](#21-용어-정리)
    - [2.1.1. hls](#211-hls)

# 개요

- 플레이어에 필요한 기능 구현하기 (재생,정지, pip, fullscreen, 미리보기 이미지 등)
- 새로고침 시 autoPlay
- hls로 영상 재생하기
- 잇톡 클론코딩

# 1. 플레이어에 필요한 기능 구현

[클론코딩 URL](https://www.youtube.com/watch?v=ZeNyjnneq_w&list=LL&index=13&t=8s)

pure javascript -> Vue 전환작업

# 2. 새로고침 시 autoPlay

## 2.1. 크롬 정책

자세한 내용은 [참고](https://developer.chrome.com/blog/autoplay/#webaudio)

자동재생 정책은 간단하다

- 음소거된 자동 재생은 언제나 허용
- 소리가 있는 자동재생은 아래와 같을 때 허용
  - 유저가 도메인과 인터렉션 했을 때 (클릭, 탭)
  - 데스크탑에서 사용자의 Media Engagement Index 값을 넘었을 때 (사용자가 이전에 소리와 함께 비디오를 재생한 경험이 있음을 의미)
  - 사용자가 모바일에서 홈 화면에 해당 사이트를 추가 했을 때
  - 사용자가 데스크탑에서 PWA를 설치한 경우
- 상담 프레임은 하위의 iframe에 소리가 있는 영상 autoplay권한을 위임할 수 있다.

### 2.1.1. Media Engagement Index (MEI)

- 미디어의 소비량이 7초 이상
- 오디오가 존재하고 음소거가 해제 되어 있어야 한다
- 비디오가 있는 탭이 활성화가 되어있어야 한다.
- 비디오 크기는 반드시 200 x 140보다 커야 한다.

### 2.1.2. 예시 시나리오

- 미디어 점수를 높혀서 자동재생이 허용되게끔
- iframe에 권한 위임
- 인터렉션을 유도
- muted된 비디오를 자동재생 되게끔.

# 2. hls로 영상 재생하기

## 2.1. 용어 정리

### 2.1.1. hls

hls = HTTP Live Streaming

- 애플이 개발하여 2009년 출시
- HTTP기반 적응 비트레이트 스트리밍 통신 프로토콜
- 표준 HTTP 트래픽을 통해 방화벽이나 프록시 서버를 경유할 수 있다.
- HTTP 기반 콘텐츠 전송 네트워크를 통해 전통적인 HTTP 서버로부터 제공 받을 수 있다.
- 암호화 메커니즘, HTTPS를 이용한 보안 키 배포를 사용
- 후반 버전은 트릭 모드 빨리감기 되감기, 자막 연동 제공

**적응 비트레이트 스트리밍이란?**

적응 비트레이트 스트리밍 = Adaptive Bitrate Streaming
과거에는 RTSP+RTP 스트리밍 기술을 이용했다.
최근에는 인터넷 등 대형분산 네트워크에서 효율적으로 동작이 필요해서
적응비트레이트 스트리밍 기술을 사용한다.

적응 비트레이트 스트리밍 특징

- 사용자의 대역폭 CPU사용률을 실시간으로 감지해 -> 미디어 스트림의 품질을 조정
- 매우 적은 버퍼링 빠른 시작 시간, 고석 연결과 저속 연결 모두에 양호한 경험을 제공

더 자세히 말하자면 적응 비트레이트 스트리밍은
소스 콘텐츠가 여러 비트레이트로 인코딩되는
HTTP 경유 비디오 스트리밍 방식의 하나이다.

**세그먼트**  
각기 다른 비트레이트 스트림은 개개가 초 단위의 여러 자그마한 부분으로 나뉜다.
세그먼트 크기는 특정 구현체에 따라 다양할 수 있으나 2초 ~ 10초가 일반적.

**동작방식**  
스트림 시작 중에 클라이언트는 보통 가장 낮은 비트레이트 스트림의 세그먼트를 요청.
네트워크 스루풋 > 세그먼트의 비트레이트 => 더 높은 비트레이트 세그먼트를 요청
네트워크 스루풋 < 세그먼트 비트레이트 => 더 낮은 비트레이트 세그먼트 요청

클라이언트의 ABR 알고리즘은 네트워크의 현재 상태를 기반으로
어느 비트레이트 세그먼트를 다운로드할지를 결정하는 주된 기능을 수행.

**네트워크 스루풋**  
network throughput
네트워크 출력이란 얼마나 많은 데이터가 단위 시간 내 목적지에
전달될 수 있는지에 대한 지표

얼마의 데이터가 1초 동안 전달되는지를 기본적인 출력률의 단위
bps(bits per second) 혹은 data packets per second로 표기
큰 출력을 가진 시스템은 bps가 아닌 Mbps, Gbps를 사용합니다
