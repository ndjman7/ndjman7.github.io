---
layout: post
title:  "Github Action으로 CI 대체하기"
author: Pando
categories: Action
tag: [Github Action, Django, pytest, CI]
cover:  "/assets/img/github_action/github_action_cover.png"
---

# Github Action으로 CI 대체하기

### 들어가기에 앞서
최근 Github에서 [파격적인 정책](https://github.blog/2020-04-14-github-is-now-free-for-teams/)을 보고, Github이 더 커지기전에 당장 Github Action를 붙여보기로 했습니다.  
[CI/CD를 제공](https://github.blog/2019-08-08-github-actions-now-supports-ci-cd/)하기에 test를 Github Action으로 사용해봅니다.  
(깃헙 만세)

### Githun Action이란?
GitHub Actions는 이벤트 기반으로 워크플로우를 생성하여, 린트, 테스트, 빌드, 배포 등을 모두 Github 안에서 관리할 수 있습니다. GitHub Actions를 사용하면 워크플로우가 저장소의 코드일 뿐이므로 공유와 재사용이 쉽습니다.  
즉, Github안에서 모든 걸 끝낼 수 있습니다.

### Django with pytest

Django 프로젝트에서 기본 django test가 아닌 **pytest**를 돌려보기로 했습니다.  
자신이 테스트할 repository로 오면 상단에 Actions 버튼을 볼 수 있습니다.  

Actions에서 [New workflow]에서 Django라는 프로젝트인 걸 알고 추천으로 Django 테스트가 상단에 노출 되어 있습니다.
<br><br>
![github_action](/assets/img/github_action/github_action_1.png)

### YAML 설정
[Set up this workflow]를 눌러 보면 기본적으로 어떻게 Github Action이 동작 될건지 YAML 파일로 예시가 나와있습니다.

```YAML
name: Django CI

on:
    push:
    branches: [ master ]
    pull_request:
    branches: [ master ]

jobs:
    build:

    runs-on: ubuntu-latest
    strategy:
        max-parallel: 4
        matrix:
        python-version: [3.6, 3.7, 3.8]

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python ${{ "{{ matrix.python-version " }}}}
        uses: actions/setup-python@v1
        with:
        python-version: ${{ "{{ matrix.python-version " }}}}
    - name: Install Dependencies
        run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Run Tests
        run: |
        python manage.py test
```
    

- name은 Github Action의 이름
- on은 어떤 환경일 때 동작할지 (push or pull request의 브랜치)
- jobs는 진행할 작업들

대충 YAML파일이 모른다고해도 해당 문법을 금방 이해할 수 있습니다.

핵심은 steps에서 자신이 하고싶은 작업을 순차적으로 명시해주면 됩니다.

내가 진행해야할 작업은 1. 자신이 사용할 브랜치 2. 파이썬 환경 설정 3. 패키지 설치 4. 테스트 수행 순으로 진행합니다.

1. pull request의 master와 develop에서만 Github Action ci 사용한다.
2. 보통 자신의 프로젝트가 파이썬의 한 버전에서 배포될 거기 때문에 3.7 하나만 사용한다. 
3. 패키지 설치는 본인의 requirments.txt가 있는 곳으로 수정해준다.
4. 마지막 테스트를 pytest로 만 바꿔준다.

```YAML
name: Django CI

on:
    pull_request:
    branches: [ master, develop ]

jobs:
    build:

    runs-on: ubuntu-latest
    strategy:
        max-parallel: 4
        matrix:
        python-version: [3.7]

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python ${{ "{{ matrix.python-version " }}}}
        uses: actions/setup-python@v1
        with:
        python-version: ${{ "{{ matrix.python-version " }}}}
    - name: Install Dependencies
        run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Run Tests
        run: |
        pytest

```

### 실행

해당 파일을 **feature/add-github-action-ci**로 만들어서 develop에 pull request로 날려봅니다.  
프로젝트 최상단에 .github/workflow/django-ci.yml을 읽어서 Github Action이 동작합니다. (이렇게 쉽게..?)
![github_action](/assets/img/github_action/github_action_2.png)
![github_action](/assets/img/github_action/github_action_3.png)
그렇지만 이내 실패합니다.

아, pytest를 requirments.txt에 추가하지 않았습니다.
![github_action](/assets/img/github_action/github_action_4.png)
<br>
파일을 수정하며 커밋하고 다시 push를 하면 pull request가 존재하므로 다시 Github Action이 동작합니다.
<br><br>

![github_action](/assets/img/github_action/github_action_5.png)
<br>
성공!

만약 `.env` 같은 파일을 사용하기 위해서는 다음 링크를 참고합니다.
- https://docs.github.com/en/actions/security-guides/encrypted-secrets

이제 제 pull request에서 check 표시로 인정받은 듯한 느낌을 받을 수 있습니다.
<br>

![github_action](/assets/img/github_action/github_action_6.png)

### 느낀 점

- 개인 개발자는 CI를 아주 쉽고 저렴하게 구축할 수 있습니다.
- Github Action은 이 기능 이외에도 무궁무진하며, 더 성장할 것 같습니다.

### 참고

[https://help.github.com/en/actions](https://help.github.com/en/actions)