---
layout: post
title:  "Docker Hub toomanyrequests: pull rate limit 해결"
date: 2022-08-03 13:43:00
author: Pando
catalog: true
header-img:  "assets/instacode.png"
header-mask: 0.4
tags:
    - Docker
---

AWS에서 Codebuild시 다음과 같은 오류가 발생하였습니다.

**Service 'nginx' failed to build: toomanyrequests: You have reached your pull rate limit. You may increase the limit by authenticating and upgrading: https://www.docker.com/increase-rate-limits.**
![toomanyrequests.png](/assets/img/docker/toomanyrequests.png)

원인을 찾아보니, Docker Hub에서 pull late limit에 걸렸습니다.

이 경우, 업그레이드를 요구하는데 AWS를 사용하고 있으니, ECR을 이용하기로 합니다.

```Dockerfile
FROM   nginx:1.21.6
...
```

- [https://www.docker.com/increase-rate-limits/](https://www.docker.com/increase-rate-limits/)

해당 이미지를 ECR에 따로 올려서, ECR 이미지를 pull받아 Codebuild를 할 수 있도록 수정하였습니다.

![ecr-nginx.png](/assets/img/docker/ecr-nginx.png)


```Dockerfile
FROM   xxxxxxxxxxxx.dkr.ecr.ap-northeast-2.amazonaws.com/nginx:1.21.6
...
```
