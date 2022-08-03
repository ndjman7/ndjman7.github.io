---
layout: post
title:  "URL에서 특정 Query String을 제거하기"
date: 2022-08-03 21:18:00
author: Pando
catalog: true
header-img:  "assets/instacode.png"
header-mask: 0.4
tags:
    - URL
---

URL에서 특정 Query String을 제거해야하는 일이 있었습니다.

다음과 같은 URL에서 UTM(utm_source, utm_medium, utm_campaign)만을 제거해보겠습니다.

**AS-IS**

[https://oneoneone.kr?utm_source=github&utm_medium=blog&utm_campaign=content&code=abc](https://oneoneone.kr?utm_source=github&utm_medium=blog&utm_campaign=content&code=abc)

**TO-BE**

[https://oneoneone.kr?code=abc](https://oneoneone.kr?code=abc)


# 코드
```python
from urllib import parse

url = "https://oneoneone.kr?utm_source=github&utm_medium=blog&utm_campaign=content&code=abc"

parsed_result = parse.urlparse(url)
# -> ParseResult(scheme='https', netloc='oneoneone.kr', path='', params='', query='utm_source=github&utm_medium=blog&utm_campaign=content&code=abc', fragment='')

parsed_query = parse.parse_qs(parsed_result.query)
# -> {'utm_source': ['github'], 'utm_medium': ['blog'], 'utm_campaign': ['content'], 'code': ['abc']}

parsed_query.pop("utm_source")
parsed_query.pop("utm_medium")
parsed_query.pop("utm_campaign")
# -> {'code': ['abc']}

parsed_query = parse.urlencode(parsed_query, doseq=True)
# -> 'code=abc'

replace_result = parsed_result._replace(query=parsed_query)
# -> ParseResult(scheme='https', netloc='oneoneone.kr', path='', params='', query='code=abc', fragment='')

replace_result.geturl()
# -> 'https://oneoneone.kr?code=abc'
```

특정 Query String만 제거하는 함수를 만들어보세요!