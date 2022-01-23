---
layout: post
title:  "Python에서 추상클래스 다루기"
author: Pando
header-img:  "assets/img/python.png"
tags:
    - Python
---

Python에 추상클래스의 구현방식을 알아봅니다.

우리가 사용할 abc 모듈은 Abstract Base Classes의 약자입니다. (개인적으로 잘 지었다고 생각합니다)

## 추상클래스 구현방법

### 선언

```python
from abc import ABC

class A(ABC):
    ...
```

Python의 경우, 다중상속이 가능하므로 여러 클래스에서 넘어온 클래스중 무엇이 진짜 정의하고싶은 추상클래스인지 알 수 없기때문에 좀더 자세하게 명시해주는 다음과 같은 방법을 쓰는 것을 추천드립니다.

```python
from abc import ABCMeta

class A(metaclass=ABCMeta):
    ...
```

### 네이밍

PEP8에도 존재하기않기 때문에 구글링에 가장 상단에 있는 답변을 봐도 정해진 네이밍은 없습니다. ([링크](https://stackoverflow.com/questions/10723839/python-naming-conventions-for-interfaces-and-abstract-classes))

결국 프로젝트를 같이하는 팀원들과 결정된 내용을 토대로 일관성 있게 유지하면 된다고 합니다.

제가 찾아보고, 괜찮게 생각하는 class명은 다음과 같습니다.

```python
# Foo의 추상클래스
from abc.meta import ABCMeta

class FooMetaclass(metaclass=ABCMeta):
    ...

# or

class BaseFoo(metaclass=ABCMeta):
    ...

# or

class AbstractFoo(metaclass=ABCMeta):
    ...
```

실제로 `djangorestframework`에서 사용하는 Serializer class의 metaclass의 네이밍으로 **SerializerMetaclass**를 사용하는 것을 알 수 있습니다.

```python
# djangorestframework package
class Serializer(BaseSerializer, metaclass=SerializerMetaclass):
    default_error_messages = {
        'invalid': _('Invalid data. Expected a dictionary, but got {datatype}.')
    }
...
```

### abstractmetod로 원하는 method 정의하기

python의 추상클래스는 어떤 메소드를 구현해야할지 강제할 수 있습니다.

다음은 method, staticemthod, classmethod를 구현하는 예제입니다.

```python
from abc import ABCMeta, abstractmethod

class FooMetaclass(metaclass=ABCMeta):

    @abstractmethod
    def method(self):
        ...

    @staticmethod
    @abstractmethod
    def static_method():
        ...

    @classmethod
    @abstractmethod
    def class_method(cls):
        ...
```

그 전에는 abc 모듈에서 제공하는 자체 absctractstaticmethod와 abstractclassmethod가 있었지만, 

버전이 올라가면서 staticmethod와 classmethod를 추가로 데코레이터 해주는 방식으로 사용하게 되었습니다.

### 추상클래스를 사용하는 이유

만약 추상클래스를 상속받아도 abstractmethod를 구현하지 않으면 오류가 발생합니다.

```python
from abc import ABCMeta, abstractmethod

class FooMetaclass(metaclass=ABCMeta):

    @abstractmethod
    def method(self):
        ...

class Foo(FooMetaclass):
    ...

class Bar:

    def __init__(self, foo):
        self.foo = foo()

    def method(self):
        self.foo.method()

bar = Bar(Foo)
```

**[오류화면]**

```bash
Traceback (most recent call last):
  File "abc_pratice.py", line 24, in <module>
    bar = Bar(Foo)
  File "abc_pratice.py", line 18, in __init__
    self.foo = foo()
TypeError: Can't instantiate abstract class Foo with abstract methods method
```

이로써 추상클래스를 사용하면 abstractmethod로 정의된 함수들은 구현된다는 보장을 받을 수 있으므로 어떤 class라도 같은 이름의 함수를 제공할 수 있습니다.

### TypeHint에서 추상클래스 인지하기

다른 클래스에 위에서 구현한 추상클래스를 의존성 주입하게하면 구현해야할 클래스를 추상클래스로 만들어서 의존성 역전 관계를 만들어 주는게 좋습니다.

그러나 Python에서는 class의 method를 알 수 없습니다. 

typehint로 명시를 해주면 추상클래스의 메소드에 접근할 수 있습니다.

다음은 예시입니다.

```python
from abc import ABCMeta, abstractmethod
from typing import Type

class FooMetaclass(metaclass=ABCMeta):

    @abstractmethod
    def method(self):
        ...

class InMemoryFoo(FooMetaclass):

    def method(self):
        return {"foo": "bar"}

class Bar:

    def __init__(self, foo: Type[FooMetaclass]):
        self.foo = foo()

    def method(self):
        self.foo.method()
```

**[Pycharm에서 추상클래스의 메소드를 인식해서 보여주는 화면]**

![type hint](/assets/img/python/abc/typehint.png)

## 참고

- [https://docs.python.org/3/library/abc.html](https://docs.python.org/3/library/abc.html)