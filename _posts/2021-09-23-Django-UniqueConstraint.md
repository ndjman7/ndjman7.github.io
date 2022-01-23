---
layout: post
title:  "Django에서 UniqueConstraint 사용하기"
author: Pando
categories: Django
tag: [Python, Django]
cover:  "/assets/img/django/banner.jpg"
---

기존에 Django에서 여러개의 필드에게 unique를 부여하고 싶을때 `unique_toghter`를 사용했었습니다.

```python
from django.db import models

class Model(models.Model):
    field1 = models.CharField(max_length=200)
    field2 = models.CharField(max_length=200)

    class Meta:
        unique_together = ['field1', 'field2']
```

그러나 Django에서는 새롭게 만든 `UniqueConstraint`를 사용하라고 권장합니다.
추후에는 `unique_toghter`를 제거하려는 목표를 가지고 있습니다.
이전과는 다르게 `condition` 속성으로 제약조건을 좀더 컨트롤할 수 있습니다.

자세한 내용은 [공식 문서](https://docs.djangoproject.com/en/3.2/ref/models/constraints/#django.db.models.UniqueConstraint)를 읽어주세요.

```python
from django.db import models

class Model(models.Model):
    field1 = models.CharField(max_length=200)
    field2 = models.CharField(max_length=200)

    class Meta:
        constraints = [
            models.UniqueConstraint(fields=['field1', 'field2'], name='please_write_down_the_name_of_the_constraint'),
        ]
```