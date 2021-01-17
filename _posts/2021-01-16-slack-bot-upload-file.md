---
layout: post
title:  "Slack Bot으로 파일 업로드 하기"
author: Pando
categories: Slack
tag: [Python, Slack]
cover:  "/assets/img/slack/thumbnail.jpg"
---

# Slack Bot으로 파일 업로드 하기

최근에 Slack Bot으로 이미지를 업로드할 일이 생겼습니다.  
Slack Bot으로 할 수 있는 일은 정말 많으며 [API Methods](https://api.slack.com/methods)를 보면 쉽게 따라 할 수 있습니다.  
> 먼저 채널에 Slack Bot이 추가되어있는지 확인하세요!

# Slack Bot에 Scope 추가하기

Slack Bot에 이미지 업로드하는 기능은 Methods에서 files 섹션에 **files.upload**를 이용합니다.  
해당 API를 사용하기 위해서는 Slack Bot에 권한(Scope)을 부여해야 한다고 나옵니다.

Bot 메뉴에서 [Basic Information] > [Add features and functionality] > [Permissions]로 들어갑니다.  
<br>
![permissions.png](/assets/img/slack/upload/permissions.png)
<br>
화면에서 내려가다 보면 Scopes 영역이 있고, [Add an OAuth Scope] 버튼을 클릭합니다.  
<br>
![scopes.png](/assets/img/slack/upload/scopes.png)
<br>
scope은 **files:wirte**를 주면 됩니다.  
scope을 추가하면 reinstall을 진행해야 하며, 다시 설치하면 됩니다.
<br><br>
![reinstall.png](/assets/img/slack/upload/reinstall.png)
<br>

# file upload API Method

[Slack API Method files.upload](https://api.slack.com/methods/files.upload)로 돌아와서 URL과 Arguments를 넣어주면 되는지 확인합니다.

저는 특정 채널에 이미지를 업로드를 할 것이기 때문에 다음과 같은 Arguments가 필요합니다.

- channels - 특정 Channel들
- file - 업로드할 이미지 파일
- initial_comment - 업로드한 파일과 함께 글이 필요한 경우 입력

이외에도 많은 Arguemnts가 있으니, 필요하시면 찾아보세요!

파일 업로드를 성공하면 다음과 같이 나오게 됩니다.
<br><br>
![success.png](/assets/img/slack/upload/success.png)


## 예시 코드

파일을 업로드하는 하는 예시 코드 입니다.  
slackclient는 파일 경로만 입력해주면 파일까지 로드해주고 채널에 올려줍니다.

직접적으로 API를 호출하는 것이 아닌, Python Package의 **slackclient==2.9.3**를 사용합니다.

```python
from slack import WebClient
client = WebClient(token="Your Token")

filepath = "/home/ubuntu/just-do-it.jpg" # Your filepath
response = client.files_upload(channels='just-do-it', file=filepath)
```