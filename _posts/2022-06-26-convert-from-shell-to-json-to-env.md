---
layout: post
title:  "Shell에서 json을 .env로 변경하기"
date: 2022-06-26 17:00:00
author: Pando
catalog: true
header-img:  "assets/instacode.png"
header-mask: 0.4
tags:
    - shell
---

Shell에서 json을 .env로 형태로 만들어야하는 경우가 있습니다.

다음과 같이 examle.json 파일이 있다고 가정합니다.

```json
{
		"GITHUB_ACTION_API_KEY": 1234,
		"AWS_ACCESS_KEY": "abcd"
}
```

`jq` 명령어를 이용하여 다음과 같이 구현합니다.

```bash
cat example.json | jq -r 'to_entries|map("\(.key)=\(.value|tostring)")|.[]' > .env
```

만약 `jq` 명령어가 설치되지않았다면 다음 링크를 참고합니다.

- [https://stedolan.github.io/jq/download/](https://stedolan.github.io/jq/download/)

다음과 같이 `.env` 이 생성된 걸 확인할 수 있습니다!

```
GITHUB_ACTION_API_KEY=1234
AWS_ACCESS_KEY=abcd
```

# 참고

- [https://stackoverflow.com/a/25378171](https://stackoverflow.com/a/25378171)