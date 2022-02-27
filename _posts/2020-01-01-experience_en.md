---
title: About Me
icon: fas fa-info-circle
layout: page
taxonomy: markup
published: false
---

# Introduce

- 서인석 (InSeock Suh)
- Email : <yoyus5@naver.com>
- Github : [github.com/ISSuh](https://github.com/ISSuh)
- Resume : [KOR](/posts/experience_kor) / [ENG](/posts/experience_en)

> For better Software

# Experience

- [Springcloud](http://www.tasio.io)
  - Autonomous Solution Develpoment Team / manager
  - System Development
  - 2018.01 ~

# Projects

## **_Develop autonomouse AI computing module and verify vehicle technology_**

### Verify autonomouse function with autonomouse computing module at vehicle

`System Analysis and Engineering 2019.05 ~ 2019.12`

### Development

- Autonomouse shuttle ananlysus and linking

  - Analysis operately data on autonomouse shuttle
  - Linking between commertial autonomouse shuttle and Springcloud's device for linking with control center

- Patent application (in South Korea)
  - Autonomouse System
  - Sensing Data transfer system and method

## **_Technology develop based verify autonomouse shuttle for verify parts of autonomous autonomouse shuttle_**

### Develop operator's monitoring system for autonomouse shuttle

`System Development 2019.03 ~ 2019.12`

### Development

- Develop function to autonomouse shuttle's operator monitoring system
  - Development of data transfer Function and Design data struct for linking control center
  - Develop video streaming server(RTSP, RTP) for camera at vehicle
  - Develop Network monitoring function(check interface, bandwidth, etc)

## **_Develop device for linking control center about vehicle infomation_**

### Solution develop for linking control center about vehicle data and sensor data

`System Development 2019.02 ~ 2019.10`

### Development

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

## **_DeepView_**

### Develop sensor data logging and data transfer system at moving object(vehicle, person, etc)

`System Development 2018.03 ~ 2018.12`

### Development

- Camera, GNSS sensor interface and getting sensor data on DrivePX2 enviroment
- Develop GUI application about Recording sensor data and transfer sensor data using QT5
  - Camera, GNSS data record
  - Camera image streaming using RTSP, RTP
  - GNSS data transfer using TCP
- Regist this software in South Korea

# Personal Projects

## **_Image2RTSP_**

### RTSP server for multi session ros image

`Personal Project` - <https://github.com/ISSuh/image2rtsp>

### Development

- Encoding ROS Image(sensor_msgs/Image) to h264 using x264 library
- Stream encoded image at live555 based RTSP server
- Access from external using Proxy server

## **_CUDA Image Process_**

### ROS node to CUDA based Image trasfortation

`Personal Project` - <https://github.com/ISSuh/CudaImageProcessing>

### Development

- GPU based ROS Image(sensor_msgs/Image) tranformation using CUDA library
  - Resize image
  - To gray scale
  - Compressed image (sensor_msgs/Image -> sensor_msgs/CompressedImage)

---

# Technical Experience

---

## Skills

- Languages
  - C
  - C++
  - Python
- Platform
  - Linux
- Development
  - Embeded system application
  - Sensor integration
  - Network
  - Browser
  - Media
- Tools
  - Docker / Docker-compose
  - Git

---

---

# Education

- Bachelor of Sience in Computer and Information Science on Korea University Sejong Campus (2011.03 ~ 2018.03)

---

---
