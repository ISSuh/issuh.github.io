---
title: About Me
icon: fas fa-info-circle
layout: page
taxonomy: markup
---

---
# Introduce
---

- 서인석 (InSeock Suh)
- Email : <yoyus5@naver.com>
- Github : [github.com/ISSuh](https://github.com/ISSuh)
- Resume : [KOR](/posts/experience_kor) / [ENG](/posts/experience_en)

> 더 나은 소프트웨어를 위해

<br/>

---
# Technical Experience
---

- Languages
  - C
  - C++
  - Python
- Platform
  - Linux
- Development Domain
  - Embeded system application
  - Sensor integration
  - Network
  - Browser
  - Media
- Tools
  - Docker / Docker-compose
  - Git

<br/>

---
# Experience & Projects
---

## [오비고](https://obigo.com/)
  - App Framework 1팀 / 연구원
  - 자체 솔루션 개발
  - 2020.03 ~

### **_Browser 및 Framework 개발_**
#### Chromium 기반 차량용 Browser 및 HTML5 기반 Web App용 Framework 개발

`솔루션 개발 2020.03 ~`

- Chromium 기반 차량용 Browser 개발
  - 각 고객사별 리눅스 기반 타겟 디바이스에 Browser 포팅 및 기능 개블
    - 고객사의 사양에 맞춰 추가 기능 개발
  - Chromium upgreade시 이에 대응하여 각 feature 포팅
  - Browser의 graphic stack
    - 타겟 디바이스 별 화면 출력 및 input 처리를 위한 window system(Wayland, X11) 연동 및 이슈 처리
    - 타겟 디바이스 별 Browser의 GPU 버그등의 이슈 처리 
  - Browser의 media stack
    - 기존 FFMPEG 기반의 media stack 구현에 대하여 Gstreamer 기반의 media stack 으로 포팅 및 개발
    - DRM 컨텐츠 재생을 위한 CDM 연동
      - Widevine DRM(L3) 포팅 및 연동 개발
      - Playready DRM(SL3000) 포팅 및 연동 개발
      - CoreTrust DRM 연동을 위한 HLS proxy 서버 포팅 및 연동 개발
        - ASIO 기반의 HLS 서버 포팅
  - Browser의 network stack
    - 타겟 디바이스의 CCU/DCU 네트워크 환경 연동
      - 타겟 디바이스와 Browser의 network namespace 환견 연동
      - 내부 DNS 서버 연동을 통한 네트워크 환경 연동

- HTML5 기반 Web App용 Framework 개발
  - 각 고객사별 요구사힝에 대하여 분석 및 기능 개발
  - 사내 Application Framework의 QT 기반 SDK 개발

---

## [스프링클라우드](http://www.tasio.io)
  - 자율주행 솔루션 개발팀 / 매니저
  - 시스템개발
  - 2018.01 ~ 2020.03

### **_자율주행 AI 컴퓨팅모듈 검증 및 차량실증기술 개발_**
#### 자율주행 컴퓨팅 모듈을 탑재하여 자율주행 기능 실증 및 실험

`시스템 분석 및 개발 2019.05 ~ 2019.12`

- 상용 자율주행 셔틀차량 분석 및 연동
  - 자율주행 차량 운행 데이터 분석
  - 사내 프로젝트인 차량 운행데이터 관제 연동 단말기 연동
- 특허 등록
  - 자율주행시스템(등록번호:1022613060000)
  - 센서데이터전송장치및방법(등록번호:1021977350000)


### **_자율주행 부품 성능검증을 위한 자율셔틀 실증 기반기술 개발_**
#### 자율주행 셔틀 차량의 운영자 모니터링 시스템 개발

`시스템 개발 2019.03 ~ 2019.12`

- 자율주행 셔틀차량의 운영자 모니터링 시스템의 일부 기능 개발
  - 차량 데이터 관제 연동을 위한 데이터 전송 규격 설계 및 기능 개발
  - 차량의 영상 전송을 위한 영상 스트리밍 서버(RTSP, RTP) 개발
  - 네트웨크 모니터링(네트워크 인터페이스, 대역폭 등) 기능 개발


### **_차량 운행데이터 관제 연동 단말기 개발_**
#### 관제 연동을 위한 차량정보 및 센서정보 연동 솔루션

`시스템 개발 2019.02 ~ 2019.11`

- 자율주행 차량 센서데이터 획득 및 차량정보 연동 소프트웨어 개발
  - Camera, GNSS, LiDAR, Radar, IMU, CAN 데이터 연동 소프트웨어 개발
- 획득한 센서데이터 및 차량정보 관제 서버로 전송 소프트웨어 개발
  - JSON 및 Google Protobuf를 통한 데이터 전송 구조 및 규격 설계
  - TCP, MQTT, Websocket, Kafka등 관제 시스템 버전에 맞춰 다양한 통신 인터페이스 사용
- 획득한 Camera 영상 전송을 위한 RTSP 서버 개발
  - Multi Camera기반 RTSP 서버 개발
  - 최적화를 위한 Multi Camera 영상 합성 및 이미지 프로세싱
- 정보 연동 소프트웨어 및 정보 전송 소프트웨어 Dockernize
  - Docker를 통한 센서별 연동 드라이버 소프트웨어및 관리
  - Docker-compos를 통한 시스템 orchestration


### **_대규모 실시간 비디오 분석에 의한 전역적 다중 관심객체 추적 및 상황 예측 기술 개발_**
#### 이동체에서(차량, 사람등)의 센서 연동 및 데이터 획득, 전송 시스템 개발

`시스템 개발 2018.03 ~ 2018.12`

- DrivePX2 보드 기반 Camera, GNSS 센서 연동 및 데이터 획득
- QT5를 이용한 데이터 획득 및 전송 GUI 어플리케이션 개발
  - Camera, GNSS 데이터 저장기능 개발
  - RTSP 서버를 통한 Camera 영상 스트리밍 기능 개발
  - TCP 서버를 통한 GNSS 데이터 전송 기능 개발
- 개발된 소프트웨어 저작권 등록


---

## [다산지앤지]()
  - 인턴
  - 테이터 분석
  - 2017.07 ~ 2017.07

### **_AMI 데이터분석_**
#### AMI(AdvancedMeteringInfrastructure) 데이터 분석

`데이터 분석 2017.07 ~ 2017.08`

- 통계를 위한 데이터 전처리
- 통계 데이터 분석 및 시각화
- SVM 알고리즘을 이용한 에너지 사용량 예측

<br/>

---
# Personal Projects
---

## **_[nLink](https://github.com/ISSuh/link)_**
#### 범용 micro service framework

- runtime
  - plugin 방식의 runtime user module load 환경 개발
  - task 기반의 async 환경 구현
- network stack
 - 기본 Socket 통신 및 HTTP 구현
- 개발중

## **_[state_manchine](https://github.com/ISSuh/state_machine)_**

#### Cross Platform(Win, Linux) 기반 State Machine header only library

- C++ 11, standard library 기반으로 쉽게 포팅가능
- Multi threading을 통해 cuncurrency state 지원


## **_[remote_display_viewer](https://github.com/ISSuh/remote_display_viewer)_**
#### linux 환경에서 screen shring을 위한 screen capture server

- gtk을 이용한 screen capture module 구현
- screen shring을 위한 Golang server 구현
- screen capture 이미지를 mpeg로 압축


## **_[Image2RTSP](https://github.com/ISSuh/image2rtsp)_**
#### ROS Image 스트림을 위한 멀티 세션 RTSP 서버 노드

- ROS Image(sensor_msgs/Image)를 x264로 압축하여 h264로 인코딩
- 인코딩된 이미지를 live555기반 RTSP 서버에서 송출
- Proxy를 내장하여 외부 접속 가능


## **_[CUDA Image Process](https://github.com/ISSuh/CudaImageProcessing)_**
#### CUDA 기반 이미지 변환 ROS 노드

- ROS Image(sensor_msgs/Image)를 CUDA 라이브러리를 이용하여 GPU기반 이미지 변환
  - 이미지 크기 변환
  - Gray scale 변환
  - JPEG 압축 (sensor_msgs/Image -> sensor_msgs/CompressedImage)

<br/>

---
# Education
---

- 고려대학교 세종캠퍼스 컴퓨터정보학과 졸업 (2011.03 ~ 2018.02)

<br/>

---
# Certificate
---

- 정보처리기사 (2017.11.24)
- TOEIC 815점 (취득일 : 2016.10)
