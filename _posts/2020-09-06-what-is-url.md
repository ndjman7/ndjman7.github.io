---
layout: post
title:  "URL이란 무엇일까?"
author: Pando
categories: CommonSense
tag: [URL, Internet, CommonSense]
cover:  "/assets/img/url/url.jpg"
---

# URL이란 무엇일까?

위키백과에서 **[URL](https://ko.wikipedia.org/wiki/URL)(Uniform Resource Locator)**을 다음과 같이 설명한다.
<br>
> 자원이 어디 있는지를 알려주기 위한 규약

즉, **1) 자원의 2) 주소**라는 뜻이다.
<br><br>
여기서 **자원(Resource)**이라는 것은 html, css, js, json, image, video등 컴퓨터에서 나타낼 수 있는 모든 데이터를 뜻한다.
<br><br>
그 다음 **주소(Locator)**는 어떤 물건이 있는 주소라고 생각하면 쉽다.
<br>
> ex) 서울특별시 강남구 대치2동 영동대로 417 B2에 안내 데스크에 있는 전화기
<br>

## URL 문법
URL을 표현하는 문법은 다음과 같이 구성되어 있다.
<br>

```
scheme:[//[user:password@]host[:port]][/]path[?query][#fragment]
```

<br>
여기서 `[]`이 의미하는 바는 상황에 따라 필수가 아니라는 뜻.
<br><br>
Web에서 각각의 의미하는 바는 다음과 같다.
<br>
1. **scheme**은 protocol이다. Web에서는 http 혹은 https를 사용한다. 그 외에도 ftp, mailto, mongodb등 많은 프로토콜이 있다.
2. **user:password**는 해당 자원에 접근할 수 있는 아이디와 비밀번호를 뜻한다.
3. **host**는 Domain name 혹은 IP로 서버의 주소를 뜻한다.
4. **port**는 해당 서버의 어느 문을 두드릴지로 http는 80 https 443을 사용한다. 그리고 브라우저에서 이는 생략된다.
5. **path**는 서버에서 자원의 위치를 나타낸다.
6. **query**는 서버로 추가적으로 보내는 파라미터로 path 바로 다음에는 `?`를 사용하고 key와 value `=`으로 구분하며 추가적인 파라미터는 `&`을 이용한다.
7. **fragment**는 anchor(닻)의 역할로써 일종의 북마크로 이용된다. 서버에는 보내지 않는다.

## URL 예시

#### 1) https://www.example.com/some.jpg

- scheme(=protocol)으로는 `https`
- host는 Domain으로 `www.example.com`
- path는 `/some.jpg`

scheme(=protocol) https로 host(Domain) www.example.com의 서버에 port는 443 생략 되어있고, path는 /some.jpg로 자원을 가리키는 URL이구나.


#### 2) ftp://pando:ra@192.168.1.234/some.mp4

- scheme(=protocol)으로는 `ftp(파일 전송 프로토콜)`
- 서버에 접속하기 위한 user로 `pando`: password로 `ra`
- ip는 `192.168.1.234`
- path는 `some.mp4`

scheme(=protocol) ftp로 서버에 접속하기 위한 user로 `pando`: password로 `ra` host(ip) 192.168.1.234의 서버에 port는 80 생략 되어있고, path는 /some.jpg로 파일 전송 URL이구나.

#### 3) mailto:ndjman7@gmail.com

- scheme(=protocol)으로는 `mailto(메일 전송)`
- ip나 Domain이 아닐 경우 `//`를 생략하고 바로 메일 주소 입력

scheme(=protocol) mailto로 ndjman7@gmail.com에 메일을 전송하는 URL이구나.

#### 4) mongodb://pando:ra@localhost:27017

- scheme(=protocol)으로는 `mongodb`
- 서버에 접속하기 위한 user로 `pando`: password로 `ra`
- host는 로컬을 뜻하는 `localhost(=127.0.0.1)`
- port는 `27017`

scheme(=protocol) mongodb로 접속하기 위한 user로 `pando`: password로 `ra` host는 로컬을 뜻하는 `localhost(=127.0.0.1)` port는 `27017` URL이구나.

<br>
**이처럼 URL은 1번을 제외하고 단순히 Web에서만 사용하는게 아니라 자원을 가리키는 여러 방법으로 사용된다.**
<br><br>

## URL은 그럭저럭 알겠고, URI와 URN은 뭐야?

URL을 배우면 따라오게 되는 URI와 URN이 있다.
<br>
의미는 각각 다음을 뜻한다.

1. URI(Uniform Resource Identifier)
2. URN(Uniform Resource Name)

인터넷에서 검색하면 다음과 같은 그림을 볼 수 있다.
![URI URL URN](/assets/img/url/uri-url-urn.png)

URI는 URL을 포함하는 개념이고 URN은 말그대로 이름으로 나타낸다는 의미이다. 
<br>
그러나 [RFC](https://ko.wikipedia.org/wiki/RFC)에서도 완벽하게 정리되지 않아서 블로그마다도 조금씩 다른 의미로 해석하고 있다.
<br><br>
따라서 Web을 사용할때는 **URL**만 기억하고 있으면 된다.
<br><br>
그래도 나처럼 URI와 URN이 궁금하다면 많은 블로그 중에 가장 자세하게 정리해둔 [블로그](https://danielmiessler.com/study/difference-between-uri-url/)를 링크로 걸어두겠다.

## 참고
- [https://ko.wikipedia.org/wiki/URL](https://ko.wikipedia.org/wiki/URL)
- [https://developer.mozilla.org/ko/docs/Learn/Common_questions/What_is_a_URL](https://developer.mozilla.org/ko/docs/Learn/Common_questions/What_is_a_URL)
