---
layout: post
title:  "Django 쿼리셋 최적화를 위한 CaptureQueriesContext"
date: 2022-10-28 23:36:00
author: Pando
catalog: true
header-img:  "assets/img/django/banner.png"
header-mask: 0.4
tags:
    - Django
---

Django에서 API가 느리다면 불필요한 쿼리로 인하여 속도가 느려졌을 수도 있습니다.
따라서 쿼리셋 최적화를 통해서 성능 개선을 합니다.

# CaptureQueriesContext
현재 어떤 쿼리가 요청되는지는 CaptureQueriesContext를 이용하면 됩니다.

```python
# 원하는 코드를 감싸줌
with CaptureQueriesContext(connection=connection) as context:
    ...
    context
```

## 예시
Content라는 모델을 조회하는 API를 조회한다고 가정해보겠습니다.


```python
from django.db import connection
from django.test import TestCase
from django.test.utils import CaptureQueriesContext
from contents.models import Content


class ContentListAPITestCase(TestCase):

    def test_콘텐츠_API_조회(self):
        # Given
        Content.objects.create()

        # When
        with CaptureQueriesContext(connection=connection) as context:
            response = self.client.get("/contents")
            context  # <- Debug
```

콘텐츠 목록을 조회하는 API에서 `context.captured_queries`로 현재 어떤 쿼리를 조회했는지 알 수 있습니다.


![CaptureQueriesContext.png](/assets/img/django/CaptureQueriesContext.png)


이로써 어떤 쿼리가 사용되는지 알 수 있기때문에 혹시나 중복 쿼리나 최적화를 할 수 있는 부분이 있는지 확인할 수 있습니다.