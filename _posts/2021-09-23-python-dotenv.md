---
layout: post
title:  "Python에서 dotenv 사용하기"
date: 2021-09-23 12:00:00
author: Pando
catalog: true
header-img:  "assets/img/python.png"
tags:
    - Python
---

```sh
pip install python-dotenv
```

django에서 사용하는 예시
```python
# settings.py
from django.core.exceptions import ImproperlyConfigured
from dotenv import load_dotenv, find_dotenv

load_dotenv(find_dotenv())


def get_env_value(env_variable: str):
    try:
        return os.environ[env_variable]
    except KeyError:
        raise ImproperlyConfigured()

SECRET_KEY = get_env_value("SECRET_KEY")
```

```
# .env
SECRET_KEY = "THISISSECRETKEY"
```

```
# .gitignore
.env
```

