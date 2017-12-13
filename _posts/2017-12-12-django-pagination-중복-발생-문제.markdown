---
layout: post
title:  "Django Pagination 중복 발생 이슈"
author: Pando
categories: Til
tags: django pagination
cover:  "/assets/instacode.png"
---

# Django Pagination이 올바르게 작동안하는 경우

## 문제점
django의 `pagination`이 잘 동작하는 줄 알았지만, 중복된 모델이 계속 노출 되는 이슈가 발생했다. local서버에서는 그런 이슈가 없었는데, 뒤늦게 발견해서 고민을 했었다.

## 해결책

### order_by의 모호성

코드가 오류가 나는게 아니라 한참을 고민했는데, order\_by때문에 나고 있다고 생각했다. order\_by를 `True/False`에 기인한 값으로 정렬했는데, 정렬기준 값들이 unique하지 않기 때문에, 이러한 이슈가 생긴다고 생각하였다. 데이터베이스의 order\_by 한 필드의 값들이 중복이 된다면 순서가 유지되지 않을 것이라는 생각! 그래서 원하는 필드인 `True/False`값으로 정렬을 한 후에, `pk` 같은 고유한 값으로 정렬해주었다.


```python
# 예시
Model.objects.order_by('not_unique', 'pk')
```

다행히 문제가 해결되었지만, 근본적인 원인을 발견한 지는 모르겠다.

## 느낀점
아 데이터베이스 공부해야 되는구나...  
혹시 제가 틀렸다면 알려주세요..