---
layout: post
title:  "네트워크에 대한 궁금증"
author: Pando
tag: [Network]
header-mask: 0.3
header-img:  "assets/img/network/main.png"
---

## 네트워크 질문 리스트
네트워크를 공부하면서 생긴 질문들입니다.

### Q. ARP 통신이 먼저 선행된 후에 데이터 송수신이 진행되는 걸까요?

ARP 요청으로 목적지 IP 주소에 대응하는 MAC 주소를 구합니다.
이 때, 그 다음 TCP/IP에서 통신(ex. TCP)은 ARP의 응답을 받고 진행하는 걸까요?

### Q. 클라우드에서는 ARP의 응답으로 MAC 주소를 어떻게 줄까요?

논리적으로 구성되어있는 AWS는 MAC 주소를 자체적으로 생성해서 주는 걸까요?

### Q. ARP로 MAC 주소를 안다면 애플리케이션에서 MAC 주소를 알 수 있을까요?
이더넷 헤더에 MAC 주소가 들어가는 것으로 파악되는데 이 경우 서버에서 클라이언트의 MAC 주소를 파악할 수 있을까요?