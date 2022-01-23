---
layout: post
title:  "Django GenericForeignKey는 언제 사용할까?"
author: Pando
header-img:  "assets/img/django/banner.png"
tags: 
    - Python
    - Django
---

# Django GenericForeignKey는 언제 사용할까?

Django에서 특정 모델이 [Generic](https://ko.wikipedia.org/wiki/%EC%A0%9C%EB%84%A4%EB%A6%AD_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)하게 모델을 가리키고 싶은 경우가 있습니다.

다음은 제 경험을 토대로 GenericForeignKey를 사용한 방법을 설명하겠습니다.

(다음은 어디까지나 예시입니다!)

## 비슷한 모델이 생성되는 상황

**반려동물**을 관리하는 사이트가 있다고 가정해봅시다.

처음에는 강아지만 관리하였기때문에 dog app에 Dog 모델만 존재했습니다.

```python
# dog/models.py

class Dog(models.Model):
    name = models.CharField(max_length=20)
    age = models.IntegerField()
```

따라서 Dog에 맞는 API들을 구축하여 서비스를 구성하고 있었습니다.

그런데 반려동물을 관리하는 사이트에서 사업이 제법 잘되서 고양이를 관리하기위해 Cat이라는 구조도 나오게 되었습니다.

```python
# cat/models.py

class Cat(models.Model):
    name = models.CharField(max_length=20)
    age = models.IntegerField()
```

그렇게 서비스가 구축되었고 사업이 또다시 번창하면서 반려동물의 종류를 다양해질 경우를 고민하게 됩니다. 

그 때마다 app을 추가하여, 하나씩 만드는 건 너무 반복적으로 코드를 생성하게 됩니다.

따라서 추후에는 반려동물이라는 모델을 구성하고 하위에 다른 모델들이 강아지인지, 고양이인지, 토끼인지 알려줄 수 있는 확장성 있는 모델로 가려고 합니다. 

왜 그전에는 생각하지 못했을까요? 그전에는 강아지만으로 될 줄 알았기 때문입니다.

그리고 그때 반려동물을 디자인하는 것은 오버 엔지니어링이었을 것입니다.

하지만 지금 당장 시간은 없죠.. (시간은 항상 부족합니다)

혹시나 궁금해 하실까봐, '종'으로 고양이, 강아지 그 외의 동물들도 구분할 수 있는 species를 추가한 반려동물 모델입니다.

```python
# animal_companion/models.py

class AnimalCompanion(models.Model):
    name = models.CharField(max_length=20)
    age = models.IntegerField()
    # 강아지, 고양이 외에도 다양한 동물들을 품을 수 있습니다.
    species = models.CharField(max_length=20)
```

다시 돌아와서, 이렇게 서비스가 운영중인 도중에 반려동물의 식사를 관리해주는 Food 서비스를 런칭하게 되었습니다. (서비스는 항상 동시다발적으로 많은 일이 일어납니다)

```python
# food/models.py

class Food(models.Model):
    name = models.CharField(max_length=20)
    calorie = models.FloatField()
```

이때 반려동물에 맞는 Food를 언제 제공했는지 관리하는 M2M 모델을 만들어야합니다. (이제야 본론 +_+)

## GenericForeignKey가 필요한 이유

강아지(Dog)에게 제공한 음식(Food)이라는 모델로 DogFood가 만들면 반려동물인 강아지가 언제 무슨 음식을 먹었는지 관리할 수 있습니다.

```python
class DogFood(models.Model):
    dog = models.ForeignKey(Dog, on_delete=models.CASCADE)
    food = models.ForeignKey(Food, on_delete=models.DO_NOTHING)
    eaten_datetime = models.DateTimeField()
```

또한 고양이(Cat)에게 제공한 음식 (Food)라는 모델로 CatFood를 만들면 됩니다.

```python
class CatFood(models.Model):
    cat = models.ForeignKey(Cat, on_delete=models.CASCADE)
    food = models.ForeignKey(Food, on_delete=models.DO_NOTHING)
    eaten_datetime = models.DateTimeField()
```

이 경우 DogFood와 CatFood라는 모델이 생기며 비슷한 코드가 작성되면서 Table도 많아지고 강아지와 고양이의 식단을 관리하는 비슷한 코드도 발생할 것입니다. 

그 사이에 토끼라는 반려동물이 추가된다면 RabbitFood가 생기겠죠

그러나 우리는 '반려동물' 모델로 리팩토링을 할 생각을 가지고 있으므로, 반려동물을 기준으로 Dog와 Cat을 한번에 가리킬 수 있게하는 GenericForeignKey를 사용합니다.

AnimalCompanionFood라는 M2M 모델 하나로 모두를 한번에 가리킬 수 있도록 설계합니다.

## Django의 ContentType 이해하기

Django에서는 [ContentType](https://docs.djangoproject.com/en/3.1/ref/contrib/contenttypes/)을 여러분도 모르게 사용하고 있습니다.

다음과 같이 아래에 기본으로 설정되어 있습니다.

```python
INSTALLED_APPS = [
    ...
    'django.contrib.contenttypes',
    ...
]
```

ContentType은 app_label과 model을 필드로 가지고 있으며, 내가 만든 app에 모델들이 관리되고 있습니다.

Dog를 예로 들어보면,

```bash
>>> from django.contrib.contenttypes.models import ContentType
>>> dog_type = ContentType.objects.get(app_label='dog', model='dog')
>>> dog_type
<ContentType: dog>
```

위와 같이 관리되고 있는 것을 알고 있습니다.

따라서 ContentType으로 Dog, Cat 모델을 가리키고 해당 모델의 PK 값을 모델의 필드에 지정해주면 하나의 M2M 모델로 모든 반려동물의 음식 기록을 할 수 있습니다.

## 드디어 GenericForeignKey

자 이제, GenericForeignKey를 이용한 M2M 모델을 설계해봅시다.

저는 '반려동물'로 모델을 리팩토링할 계획이 있으므로, app을 미리 반려동물(animal_companion)을 만들어 둡니다.

```python
# animal_companion/models.py
from django.contrib.contenttypes.fields import GenericForeignKey
from django.contrib.contenttypes.models import ContentType

class AnimalCompanionFood(models.Model):
    animal_companion_type = models.ForeignKey(ContentType, on_delete=models.CASCADE)
    animal_companion_id = models.PositiveIntegerField()
    animal_companion = GenericForeiginKey(ct_field='animal_companion_type', fk_field='animal_companion_id')

    food = models.ForeignKey(Food, on_delete=models.DO_NOTHING)
    eaten_datetime = models.DateTimeField()
```

이로써 animal_companion_type으로 Dog, Cat 모델을 지칭할 수 있고, anmal_companion_id로 pk를 가리켜 어떤 반려동물이더라도 하나의 모델로 음식을 관리할 수 있습니다.

animal_companion은 실제로 Table에 Column이 존재하는 것이 아니라, Code상에서만 존재합니다.

## 역참조를 위한 GenericRelation

Dog, Cat에서도 어떤 반려동물이 음식을 먹었는지 조회하기위해선 역참조를 할 수 있어야합니다.

그 때 GenericRelation을 사용합니다.

```python
# dog/models.py

class Dog(models.Model):
    name = models.CharField(max_length=20)
    age = models.IntegerField()

    foods = GenericRelation(
        content_type_field='animal_companion_type',
        object_id_field='animal_companion_id',
    )
```

반려동물마다 만들어줘야하기 때문에 Cat에도 똑같이 만들어 줍니다.

```python
# cat/models.py

class Cat(models.Model):
    name = models.CharField(max_length=20)
    age = models.IntegerField()

    foods = GenericRelation(
        content_type_field='animal_companion_type',
        object_id_field='animal_companion_id',
    )
```

이렇게 되면 Dog에서도, Cat에서도 같은 ORM으로 조회할 수 있습니다.

```python
# Dog에서 조회하기
dog = Dog.objects.get(name="바리")
dog.foods.all()

# Cat에서 조회하기
cat = Cat.objects.get(name="냥이")
cat.foods.all()
```

## 끝으로

GenericForeignKey는 코드를 간결하게 해주고, Table을 중복 생성을 방지한다는 점이 있지만 animal_companion에게 Indexing을 할 수도 없고, DB 구조도 복잡해집니다.

어디까지나, 
1. 리팩토링으로 더 나은 구조(반려동물로 모델을 통합)
2. 과도기적인 부분(Food 서비스는 그 전에 출시 되어야함)

M2M 모델을 고려한 것이므로 당분간 사용할 것으로 생각하여 적용한 것입니다!

왜 리팩토링을 먼저 하지않느냐?라는 질문에는 다음과 같이 답할 수 있습니다.

1. 나중에는 당연히 필요하지만 현재 더 급한 우선순위의 문제들이 있습니다. (반려동물 음식 서비스)
2. 구조를 바꾼다는 건, 달리는 열차에서 바퀴를 문제없이 갈아끼우는 일입니다. (말처럼 쉽지않다는 뜻이죠!)

결국 GenericForeiginKey는 여러 고민 끝에 사용하게 된 것이므로 무분별하게 낭비하는 것은 좋지 않습니다.