---
layout: post
title:  "파이썬 크롤링으로 자식 태그를 제외한 HTML 태그 text 가져오기"
author: Pando
header-img:  "assets/img/crawler/web_crawler.png"
tags: 
    - Crawling 
    - Python
    - BeautifulSoup
---

# 파이썬 크롤링으로 자식 태그를 제외한 HTML 태그 text 가져오기


다음과 같은 경우, [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#navigating-the-tree)을 이용하여 파싱한 후 텍스트만 추출하면 다음과 같이 나온다.
<br>

```html
<div id="target">
    <div id="except">
        이 내용은 가져오지마
    </div>
    이 내용을 가져와
</div>
```
### 코드

```python
from bs4 import BeautifulSoup
from bs4.element import NavigableString

html = """
<div id="target">
    <div id="except">
        이 내용은 가져오지마
    </div>
    이 내용을 가져와
</div>
"""

soup = BeautifulSoup(html, 'html.parser')
target_tag = soup.select_one("#target")
print(target_tag.text)
```

### 출력

```text


        이 내용은 가져오지마
    
    이 내용을 가져와

```

하지만 우리는 자식 태그를 제외한 태그의 내용만 가져오고 싶다면 어떻게 해야할까?
<br>
이 경우, BeautifulSoup 공식문서를 읽어보면 총 4가지의 objects로 존재함을 알 수 있다.
<br>
1. Tag
2. NavigableString
3. BeautifulSoup
4. Comment

따라서 해당 태그를 for문으로 순회하면서 1. 어떤 object에 2. 어떤 내용이 있는지 출력해본다.

### 코드

```python
soup = BeautifulSoup(html, 'html.parser')
target_tag = soup.select_one("#target")
for bs_object in target_tag:
    print("[object]", type(bs_object))
    print("[repr]")
    print(bs_object)
    print("-----")
```

### 출력

```
[object]
<class 'bs4.element.NavigableString'>
-----
[repr]


-----
[object]
<class 'bs4.element.Tag'>
-----
[repr]
<div id="except">
        이 내용은 가져오지마
    </div>
-----
[object]
<class 'bs4.element.NavigableString'>
-----
[repr]

    이 내용을 가져와

-----
```

해당 태그는 다음과 같이 구성되어 있었다.

1. [열린 태그 ~ 자식 태그] `NavigableString`
2. 자식 태그는 `Tag`
3. [자식 태그 ~ 닫힌 태그] `NavigableString`

따라서 for문을 순회하면서 NavgableString 태그인 경우를 가져오면 내부 태그는 없앨 수 있다.

### 완성 코드

```python
from bs4 import BeautifulSoup
from bs4.element import NavigableString

html = '''
<div id="target">
    <div id="except">
        이 내용은 가져오지마
    </div>
    이 내용을 가져와
</div>
'''

soup = BeautifulSoup(html, 'html.parser')
target_tag = soup.select_one("#target")
target_text_without_child_tags = [
    bs_object
    for bs_object
    in target_tag
    if isinstance(bs_object, NavigableString)
]
text = "".join(target_text_without_child_tags)
print(text)
```

### 실행 결과

```text


    이 내용을 가져와

```

### 참고
[BeautifulSoup 공식문서](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#navigating-the-tree)
<br>
[stackoverflow](https://stackoverflow.com/questions/30159020/get-text-of-html-tags-without-text-of-inner-child-tags)
<br>
