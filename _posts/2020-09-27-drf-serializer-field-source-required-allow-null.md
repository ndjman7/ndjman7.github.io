---
layout: post
title:  "DRF serializer fields source, required, allow_null에 대하여"
author: Pando
categories: DRF
tag: [DRF, serializer]
cover:  "/assets/img/drf/main.png"
---

# 들어가기에 앞서

DRF에는 serializer라는 개념이 있다.

그 중에도 ModelSerializer라는 강력한 개념은 model에 field명을 그대로 serializer에 가져올 수 있다.

이 때 가져오는 것은 단순히 필드명이 아닌 필드에 적용된 속성도 가져올 수 있다는 점이다.

그러나 ModelsSerializer의 경우에도 field를 직접 정의하는 경우가 있는데, Serilaizer argment인 source, required, allow_null에 대한 설명과 오류가 발생한 상황에 대하여 공유한다.

# source, required, allow_null 설명

## source란?

[drf 공식 홈페이지](https://www.django-rest-framework.org/api-guide/fields/#source)에 대한 설명은 다음과 같다.

> 필드를 채우는데 사용할 속성의 이름입니다.
> 자신의 메소드일 수도 있고, ex) URLField(source='get_absolute_url')
> 
> dotted 표기법으로 속성을 탐색할 수 있습니다. ex) EmailField(source='user.email')
> 
> `source='*'`의 경우 전체 객체가 통과되어야할 때 나타낼때 사용합니다. 이는 중첩된 표현을 사용할 때 유용합니다.  (아마 foreign key로 연결된 다른 object를 표현할 때 유용한 것 같다)

## required란?

[drf 공식 홈페이지](https://www.django-rest-framework.org/api-guide/fields/#required)에 대한 설명은 다음과 같다.

> 일반적으로 deserialization 중에 필드가 제공되지 않으면 오류가 발생합니다. 역 직렬화 중에이 필드가 필요하지 않으면 `False`로 설정하십시오.
> 
> 이 값을 `False`로 설정하면 인스턴스를 직렬화 할 때 출력에서 객체 속성 또는 사전 키를 생략 할 수도 있습니다. 키가 없으면 단순히 출력 표현에 포함되지 않습니다.
> 
> Default는 `True`입니다.

## allow_null이란?

[drf 공식 홈페이지](https://www.django-rest-framework.org/api-guide/fields/#allow_null)에 대한 설명은 다음과 같다.

> 일반적으로 `None`이 serializer 필드에 전달되면 오류가 발생합니다. `None`이 유효한 값으로 간주되어야하는 경우이 키워드 인수를 `True`로 설정하십시오.
> 
> 명시적인 기본값없이 인수를 True로 설정하면 직렬화 출력에 대해 기본값이 null임을 의미하지만 입력 역 직렬화에 대한 기본값을 의미하지는 않습니다.
> 
> Default는 `False`입니다.

# 상황

특정 모델에 필드명을 API 파라미터에서 좀더 명시적이고 간단하게 쓰고 싶은 생각이 있었다.

그래서 ModelSeirlaizer에서 model의 field를 그대로 사용한게 아니라, `source` 옵션을 이용하여 field명을 좀 더 간단하게 바꿔주었다.

`source` 옵션이 적용되면 ModelSerializer에서 기존의 모델 필드 옵션을 그대로 가져와 적용해줄 줄 알았다.

그래서 데이터가 넘어오지 않아도 되는 `required`의 옵션만 추가해주었는데, null로 들어오는 경우에도 `allow_null`을 넣어주어야 했던 것이다.

# 코드

현재 테스트한 버전은 다음과 같다.

```python
Django==3.1.1
djangorestframework==3.11.1
```

Test 모델에 something이라는 필드가 있고 `null=True` 속성이 있다고 설정한다.

```python
from django.db import models

class Test(models.Model):
	something = models.CharField(max_length=200, null=True)
```

이 경우, ModelSerializer의 경우에는 해당 모델의 필드만 명시해주면 Serializer가 해당 모델의 필드 속성을 그대로 가져와서 설정을 해준다.

```python
from rest_framework import serializers

from practice.models import Test

class TestSerializer(serializers.ModelSerializer):

    class Meta:
        model = Test
        fields = (
            "something",
        )
```

`/something/`이라는 POST API를 만든다. 단순히 serializer 값을 검증하고 save하는 내용이다.

```python
class SomethingAPIView(APIView):

    def post(self, request):
        serializers = TestSerializer(data=request.data)
        if not serializers.is_valid():
            return Response(data=serializers.errors, status=status.HTTP_400_BAD_REQUEST)

        serializers.save()
        return Response(data=serializers.data, status=status.HTTP_201_CREATED)
```


http 파일로 간단하게 null을 넘겨본다.

```
POST http://localhost:8000/something/
Content-Type: application/json

{
  "something": null
}
```

이 경우에는 null 값이 정상적으로 들어간다. 이 경우 Serializer는 모델의 field를 바라보고, `null=True` 속성이 적용되어 저장된 것을 알 수 있다.

나는 여기서 `something`이라는 필드명이 명시적이지 않다고 생각했고, `some`으로 줄여서 넘겨주기 위하여 serializer의 argument의 source를 사용했다. 이 때 해당 필드값이 필요없을 수 있으니 `required=False`을 주었다.

```python
class TestSerializer(serializers.ModelSerializer):

    some = seriailizersome = serializers.CharField(
        source="something", required=False
    )

    class Meta:
        model = Test
        fields = (
            "some",
        )
```

source로 something이라는 필드를 자동으로 바라보는 줄 알고, 나는 Test 모델의 something 필드에 존재하는 `null=True` 옵션이 자동으로 적용된다고 생각했다.

```
POST http://localhost:8000/something/
Content-Type: application/json

{
  "some": null
}
```

이 경우 다음과 같은 응답이 온다.

```python
{
  "some": [
    "This field may not be null."
  ]
}
```

source는 해당 필드에 데이터를 바라보게 해주는거지, field에 적용된 속성까지 검증해주지않는 것을 알 수 있다.

명시적인 null을 허용하는 allow_null 속성을 추가해주었다.

```python
class TestSerializer(serializers.ModelSerializer):

    # allow_null 속성 추가
    some = seriailizersome = serializers.CharField(
        source="something", required=False, allow_null=True
    )

    class Meta:
        model = Test
        fields = (
            "some",
        )
```

정상적으로 들어간다.

# 결론

- source는 모델에 맞는 field로 가리키지, field의 속성까지 자동으로 검증하지 않는다.
- required는 데이터를 생략 가능하게 해주는 것, allow_null은 명시적인 null 값을 허용하는 것이다.