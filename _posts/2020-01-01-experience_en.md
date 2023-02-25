---
title: About Me
icon: fas fa-info-circle
layout: page
taxonomy: markup
---

---
# About Me
---

- 서인석 (InSeock Suh)
- Email : <yoyus5@naver.com>
- Github : [github.com/ISSuh](https://github.com/ISSuh)
- Resume : [KOR](/posts/experience_kor) / [ENG](/posts/experience_en)

> For better Software

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

## [Obiso](https://obigo.com/)
  - App Framework 1 team / researcher
  - Software Engineering
  - 2020.03 ~

### **_Develoment Browser and Framework_**
#### Develoment chromium based browser for vehicle and Framework for HTML5 based web application

`Software Engineering 2020.03 ~`

- Develoment chromium based browser for vehicle
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

- Develoment Application Framework for HTML5 based web application
  - Requirements Analysis for several customer and development feature
  - Development QT based SDK for Application Framework product

---

## [Springcloud](http://www.tasio.io)
  - Autonomous Solution Develpoment Team / manager
  - System Development
  - 2018.01 ~ 2020.02

### **_Develop autonomouse AI computing module and verify vehicle technology_**
#### Verify autonomouse function with autonomouse computing module at vehicle

`System Analysis and Engineering 2019.05 ~ 2019.12`

- Autonomouse shuttle ananlysus and linking
  - Analysis operately data on autonomouse shuttle
  - Linking between commertial autonomouse shuttle and Springcloud's device for linking with control center
- Patent application (in South Korea)
  - Autonomouse System (publish number : 1022613060000)
  - Sensing Data transfer system and method (publish number : 1021977350000)


### **_Technology develop based verify autonomouse shuttle for verify parts of autonomous autonomouse shuttle_**
#### Develop operator's monitoring system for autonomouse shuttle

`System Development 2019.03 ~ 2019.12`

- Develop function to autonomouse shuttle's operator monitoring system
  - Development of data transfer Function and Design data struct for linking control center
  - Develop video streaming server(RTSP, RTP) for camera at vehicle
  - Develop Network monitoring function(check interface, bandwidth, etc)


### **_Develop device for linking control center about vehicle infomation_**
#### Solution develop for linking control center about vehicle data and sensor data

`System Development 2019.02 ~ 2019.10`

- Develop getting autonomouse vehicle's sensor data and autonomouse vehicle's information
  - Develop Sensor(Camera, GNSS, LiDAR, Radar, IMU, CAN) Driver for getting data
- Develop data transfer software about getting sensor data and vehicle infomation
  - Design data structure for communication using JSON, Google Protobuf
  - Using variouse communication protocol such as TCP/UDP, MQTT, Websocket, Kafka for communicating control center
- Develop RTSP server for image streaming at vehicle
  - Develop multi Camera, multi session RTSP server
  - multi Camera Image processing and fusion for optimzation
- Dockernize sensor driver software and data transfer software
  - Manage several sensor driver software and version using Docker
  - Docker Orchestration using Docker-compos


### **_DeepView_**
#### Develop sensor data logging and data transfer system at moving object(vehicle, person, etc)

`System Development 2018.03 ~ 2018.12`

- Camera, GNSS sensor interface and getting sensor data on DrivePX2 enviroment
- Develop GUI application about Recording sensor data and transfer sensor data using QT5
  - Camera, GNSS data record
  - Camera image streaming using RTSP, RTP
  - GNSS data transfer using TCP
- Regist this software in South Korea

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
#### general micro service framework

- runtime
  - implement plugin based user module load environment
  - implement task based async environment
- network stack
 - implement basic socket tracnfer
 - implement HTTP stack
- in development


## **_[state_manchine](https://github.com/ISSuh/state_machine)_**
#### State Machine header only library for Cross Platform

- C++ 11, easy port, standard library based libaray
- support concurrency state using multi theading


## **_[remote_display_viewer](https://github.com/ISSuh/remote_display_viewer)_**
#### screen capture server for screen shring on Linux platform

- implement screen capture module using gtk
- implement Golang server for screen shareing
- compressed screen capture image using MJPEG


## **_[Image2RTSP](https://github.com/ISSuh/image2rtsp)_**
### RTSP server for multi session ros image

- Encoding ROS Image(sensor_msgs/Image) to h264 using x264 library
- Stream encoded image at live555 based RTSP server
- Access from external using Proxy server


## **_[CUDA Image Process](https://github.com/ISSuh/CudaImageProcessing)_**
### ROS node to CUDA based Image trasfortation

- GPU based ROS Image(sensor_msgs/Image) tranformation using CUDA library
  - Resize image
  - To gray scale
  - Compressed image (sensor_msgs/Image -> sensor_msgs/CompressedImage)

<br/>

---
# Education
---

- Bachelor of Sience in Computer and Information Science on Korea University Sejong Campus (2011.03 ~ 2018.03)

<br/>

---
# Certificate
---

- 정보처리기사 (2017.11.24)
- TOEIC 815 (2016.10)