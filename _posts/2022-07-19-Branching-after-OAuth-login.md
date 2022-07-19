---
layout: post
title:  "OAuth 로그인 후 분기처리하기"
date: 2022-07-19 22:00:00
author: Pando
catalog: true
header-img:  "assets/instacode.png"
header-mask: 0.4
tags:
    - Cookie
    - OAuth
---

## 들어가기에 앞서

OAuth 로그인 후, 진입한 상황마다 분기처리를 하고 싶은 경우가 있습니다.
- 회원가입인 경우는 홈화면으로 이동
- 계정 연동이라면 연동 후 연동화면으로 이동

웹사이트에서 어떻게 상황마다 분기처리를 할지 알아봅시다.

## OAuth 진행
OAuth 로그인을 할 때 다음과 순서는 다음과 같습니다.

1. 자체 사이트에서 OAuth 사이트에서 제공한 client_id를 링크에 붙여서 redirect합니다.
2. OAuth 사이트에서 로그인을 합니다.
3. 미리 정의한 redirect url로 자체 사이트로 돌아옵니다.

이제 redirect url로 돌아왔을 때, 상황마다 분기처리를 하려면 어떻게 해야할까요?

## OAuth 진입 전에 쿠키🍪 심기
1번에서 OAuth 사이트로 넘어가기 전에 미리 정의한 Cookie를 심는 겁니다.

순서는 다음과 같습니다.

1. 자체 사이트에서 쿠키를 설정하여, html을 반환합니다.
2. **브라우저에 쿠키🍪를 심고, 자체 사이트의 OAuth 인증 페이지로 넘깁니다.**
3. 자체 사이트에서 OAuth 사이트에서 제공한 client_id를 링크에 붙여서 redirect합니다.
4. OAuth 사이트에서 로그인을 합니다.
5. 미리 정의한 redirect url로 자체 사이트로 돌아옵니다. **이 때 미리 설정한 Cookie를 통하여 분기처리를 진행합니다.**

유저에게는 찰나의 순간이지만, 서버에는 한번 더 요청을 처리하는 작업이 있습니다.

1번의 반환할 html은 다음과 같습니다.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta http-equiv="refresh" content="0; URL=https://example.com/login/google">
  </head>
  <body>
  </body>
</html>
```

이로써 1번의 진입점으로 인해 상활별로 다른 Cookie의 값으로 OAuth 페이지를 다녀와서도 어떤 처리를 이어할지 파악할 수 있게 되었습니다.

# 참고

[https://www.w3.org/TR/2016/NOTE-WCAG20-TECHS-20161007/H76](https://www.w3.org/TR/2016/NOTE-WCAG20-TECHS-20161007/H76)