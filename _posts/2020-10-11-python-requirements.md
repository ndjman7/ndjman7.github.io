---
layout: post
title:  "Python Pacakge requirements.txt로 관리하기"
author: Pando
header-img:  "assets/img/requirements/requirements-txt.png"
tags: 
    - Python
    - Package
---

## Python Package 관리는 requirements.txt 라는 파일로 합니다.

Project는 내가 만든 코드와 여러가지 Python Package들로 이루어집니다.

소스코드를 관리할 때는 Package 소스코드는 따로 관리하지 않습니다.

만약 내 소스코드 이외의 Package 소스코드를 같이 관리하게 된다고 가정하면, 모든 Porject는 매우 무거워질 것입니다.

![project](/assets/img/requirements/project.png)

따라서 어떤 Packake를 사용하는지 version을 명시한 목록만 txt 파일로 작성합니다.

Python에서도 어떤 Package를 사용하고 있는지 명시해주는 requirements.txt라는 파일이 존재합니다.

![requirements.txt](/assets/img/requirements/requirements-txt.png)

만약 내가 가상환경 안에서 `Django`라는 Pacakge를 사용한다고 가정한다면 requirements.txt에 담아주어야 합니다.

내가 현재 프로젝트에 설치한 Package 목록은 다음과 같은 명령어로 알 수 있습니다. 

```bash
pip freeze
```

requirements.txt는 재료를 담아놓은 것 처럼 패키지 이름과 version을 명시하고 있는데, `Django`를 설치했을 경우 당음과 같이 명시해주어야합니다.

```
[requirements.txt]
Django==3.1.2
```

이렇게 되면 어떤 Package가 사용되었는지 알 수 있고 프로젝트 코드는 훨씬 더 가벼워집니다.

그러나 Package를 설치할 때 마다, requirements.txt를 넣어주는게 제일 좋은 방법이지만 잦은 설치로 목록을 누락시킬 수 있습니다. 

그럴 경우 다음과 같은 명령어로 requirements.txt를 현재 가상환경에 설치된 최신화된 목록으로 업데이트할 수 있습니다.

```bash
pip freeze > requirements.txt
```

그러나, 이렇게 되면 내가 설치하지않은 Package도 같이 명시될 수 있습니다.

현재 `Django==3.1.2` 을 기준으로 requirments.txt를 만들어보면 다음과 같은 Package도 명시됩니다.

```
asgiref==3.2.10
Django==3.1.2
pytz==2020.1
sqlparse==0.4.1
```

이유는 Package도 결국 하나의 Project고 그 안에 또 다른 Package를 사용하기 때문입니다.

![package](/assets/img/requirements/package.png)

Package 설치는 다음과 같은 명령어로 새로운 환경에 쉽게 설치할 수 있습니다.

```bash
pip install -r requirements.txt
```

# 마무리

내 프로젝트에는 오로지 자신이 만든 코드만 존재해야하고 **Package는 requirements.txt에 version과 함께 명시**합니다.

이 때 Package의 의존성 Package까지 같이 명시하면 requirements.txt는 복잡해지므로, 자신이 설치한 Package만 명시해줍니다.

그리고 Project를 세팅할 때에는 requirements.txt 파일을 이용하여 Package들을 한번에 설치하는 것입니다.

# 같이 읽으면 좋은 글
<a href="https://www.fullstackpython.com/application-dependencies.html" target="_blank">application-depencies</a>