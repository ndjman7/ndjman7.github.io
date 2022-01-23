---
layout: post
title:  "Python에서 정수 실수 구분하기"
author: Pando
header-img:  "assets/img/python.png"
tags: 
    - Python
---

# Python에서 정수 실수 구분하기

Python에서는 나눗셈하면 return 되는 결괏값이 항상 float입니다.

정수로 나누어떨어져도 float가 됩니다. ex) 4 / 2 = 2.0

Python에서 나눗셈 이후 float를 int로 표현하고 싶을 때는 float에 `is_integer` 를 사용하면 됩니다.

## 예시

나눗셈을 했을 경우, float일 때 소수점 둘째 자리까지 표현하고 int인 경우에는 int로 표시하는 예제입니다.

### 예시 코드

```python3
def dvide(a, b):
    c = a / b
    # int라면 int를 반환
    if c.is_integer():
        return int(c)
    # float라면 소수점 둘째 자리까지 반환
    return round(c, 2)

divide(1, 3)
# 0.33

divide(4, 2)
# 2
```
