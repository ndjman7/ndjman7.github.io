---
layout: post
title:  "Mac에서 나의 Public IP 알아내기"
author: Pando
header-img:  "assets/header_image.jpg"
tags: 
    - Shell
    - IP
---

# Mac에서 나의 Public IP 알아내기

현재 나의 Public IP를 알아내기위해, Naver와 같은 사이트에서 `내 아이피 주소 확인`을 입력한 경험이 있을 것이다.

![Naver public ip](/assets/img/naver-public-ip.png)

ifconfig라는 명령어를 Shell에 입력해도 돌아오는 것은 Private IP일뿐..

맥북에 네트워크를 접속해도 내부 IP만 보인다.  

![Mac network](/assets/img/mac-network.png)

굳이 브라우저로 사이트를 방문하지 않더라도 Shell에서 다음 명령어를 치면 Public IP가 반환된다.

```Shell
> curl ifconfig.me
121.140.5.214
```

ifconfig.me 서버가 Public IP만 반환해준다.  
사실 curl만 쓸 수 있다면 어디서든 Public IP를 알아낼 수 있다.  
앞으로는 사이트를 방문하지 말자.  
