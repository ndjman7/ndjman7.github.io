---
layout: post
title:  "Smart UI 디자인 패턴이란?"
date: 2022-01-29 22:00:00
author: Pando
catalog: true
header-img:  "assets/img/design_pattern/smart_ui.jpeg"
header-mask: 0.4
tags:
    - Design Pattern
---

에릭 에반스의 도메인 주도 설계에서는 **“Smart UI(지능형 UI) Anti-Pattern”**이라는 용어가 나옵니다.

저자는 도메인 주도 설계와는 서로 상호배타적이기때문에 Anti-Pattern이라고 합니다.

여기서 Smart UI(지능형 UI) Anti-Pattern이란 무엇일까요?

# Smart UI Design Pattern이란

Smart UI Design Pattern은 애플리케이션에서 데이터의 입력과 표시에 영향을 받고 비즈니스 로직은 UI에 작성되는 것을 말합니다.

또한 UI에서 관계형 DB에 직접적으로 상호작용합니다.  

대표적으로 과거의 PHP 코드에서 종종 볼 수 있습니다.  

UI가 Smart하다는 건 UI에 비즈니스 로직이 있고 인프라에 직접적으로 접근하는 한마디로 계층 구분이 없는 코드를 의미합니다.  

쉽게 말하면 UI에서 계산도 하고 DB에 접근을 한다는 의미죠.

요즘엔 프레임워크들이 대부분 MVC Design Pattern기반이기때문에 어느정도 계층을 분리합니다.

MVC Design Pattern과 비교해서 보면 다음과 같습니다.

![Smart-UI.jpeg](/assets/img/design_pattern/smart_ui.jpeg)

어렵게 이야기했지만 Smart UI Design Pattern란 계층 구분없이 만들면 됩니다.

처음에 생산속도가 가장 빠르지만 시간이 지날 수록 유지보수와 확장이 어려워지는 건 많은 분들이 경험하셨을 거에요.

이런 Design Pattern이 무조건 나쁘다고 볼 수 없는 건, 다음과 같이 사용할 수 있습니다.
- 이벤트성 페이지 
- 제품의 가능성을 PoC로 검증

중요한 건 시간과 자원을 고려하여 제품의 생애주기를 파악하고 적재적소에 Design Pattern을 사용하는 능력이라고 생각합니다.

요즘에는 지능형 안티라는 말이 있어서 그런지 지능형 UI라고 하니까 부정적인 어감이긴 하네요!

# 참고 자료

- [https://stackoverflow.com/questions/68195463/what-is-the-smart-ui-anti-pattern](https://stackoverflow.com/questions/68195463/what-is-the-smart-ui-anti-pattern)