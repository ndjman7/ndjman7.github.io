---
layout: post
title:  "로컬 개발환경에서 서버 개발환경의 쿠키 이용하기 (ngrok)"
date: 2022-10-28 23:36:00
author: Pando
catalog: true
header-img:  "assets/img/django/banner.png"
header-mask: 0.4
tags:
    - cookie
---

프론트엔드는 자주 UI를 변경하고 확인을 합니다.  
매번 개발서버로 반영하고 확인하는 것은 생산성의 저하를 가져옵니다.  
로컬 개발환경에서 서버 개발환경을 쉽게 이용하는 것을 다룹니다.  

# 서버의 쿠키 사용하기
서버의 주소를 `api.example.com` 이라고 가정하겠습니다.  
실제로 쿠키를 사용할 프론트엔드 서버의 주소를 `front.example.com` 으로 가정합니다.  

서버에서 넘어온 쿠키는 `.example.com` 으로 서브 도메인까지만 사용할 수 있습니다.  
따라서 프론트엔드 개발 서버는 서버가 심은 쿠키를 사용할 수 있습니다.  
그러나 프론트엔드의 로컬 개발환경은 `localhost` 이기 때문에 쿠키를 가져올 수 없습니다.  

기존에는 프론트엔드의 개발 환경에 계속 배포를 하면서 확인을 했습니다.  
그러나 로컬 개발환경에서 바로 접근을 할 수 있다면 생산성이 향상 될 것입니다.  

이 때, `ngrok`를 이용합니다.

# ngrok로 로컬 개발환경 주소에 도메인 연결하기
ngrok는 로컬 개발환경을 외부에서 접근할 수 있습니다.  
그러나, ngrok의 Free에서는 계속 도메인이 주기적으로 바뀌기 때문에 도메인을 연결해도 바뀌게 됩니다.  

> https://\<random-string\>.\<region\>.ngrok.io/

![ngrok1.png](/assets/img/cookie/ngrok1.png)

따라서 Pro를 결제하고, Custom Domain을 만듭니다.  
예시 Custom Domain은 `local.example.com` 입니다.  

![ngrok1.png](/assets/img/cookie/ngrok2.png)

사용이 가능하면 설정시 CNAME을 받을 수 있습니다.

서브 도메인으로 CNAME을 연결해주면 `https://local.example.com` 을 로컬 개발환경으로 사용할 수 있습니다.  
또한 `example.com` 의 서브 도메인이므로 `api.example.com` 에서 발급한 쿠키를 그대로 사용할 수 있습니다.  

![ngrok1.png](/assets/img/cookie/ngrok3.png)

# 명령어
명령어는 다음과 같습니다.
```sh
ngrok http --region=jp --hostname=local.example.com 3000
```

# 정리
로컬 개발환경(`localhost`)에서는 서버(`api.example.com`)에서 심은 쿠키(`.example.com`)를 사용할 수 없습니다.  
`ngrok`를 이용하여 로컬 개발환경에 서브 도메인(`local.example.com`)을 부여합니다.  

> https://local.example.com -> http://localhost:3000  

이로써, 로컬 개발환경(`local.examlple.com`)에서 쿠키를 사용할 수 있게 됩니다.

생산성 향상을 위해, 로컬 개발환경에서 서버 개발환경까지 바로 배포하지않고 `ngrok`를 사용합시다.  
~~약간의 비용은 청구됩니다.~~

