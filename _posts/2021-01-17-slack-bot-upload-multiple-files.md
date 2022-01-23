---
layout: post
title:  "Slack Bot으로 파일 여러 개 업로드 하기"
author: Pando
header-img:  "assets/img/slack/thumbnail.jpg"
header-mask: 0.4
tags: 
    - Python
    - Slack
---

# Slack Bot으로 파일 여러 개 업로드하기

지난 시간에 [Slack Bot으로 파일을 올리는 법](https://ndjman7.github.io/slack/2021/01/16/slack-bot-upload-file.html)을 알아봤습니다.

그러나 이렇게 끝나면 너무 간단하죠..?

이번에는 단순히 파일을 하나만 올리는 일이 아니라, Slack Bot으로 하나의 채팅에 여러 개의 파일을 올릴 일이 필요해졌습니다.

[API Methods](https://api.slack.com/methods)를 찾아봐도 file.uploads에는 하나의 파일만 업로드가 가능하고 여러 개를 올릴 수 있는 방법은 없었습니다.

구글링을 하여, 나와 같이 여러 개의 파일을 올리는 방법에 대한 질의응답이 있는 [stackoverflow](https://stackoverflow.com/questions/59939261/send-multiple-files-to-slack-via-api)를 발견하였습니다.

해당 내용에 따르면 방법은 다음과 같습니다.

## 1. 파일을 미리 업로드하여 link만 가져옵니다.

[file.uploads](https://api.slack.com/methods/files.upload) API를 이용하여 채널에 올리는 것이 아닌 Slack Workspace에 올려 link를 가져올 수 있습니다.

Response에 file > permalink 에서 값을 가져옵니다.

```python
from slack import WebClient
client = WebClient(token="Your Token")

filepath_list = [
    "/home/ubuntu/just.jpg",
    "/home/ubuntu/do.jpg",
    "/home/ubuntu/it.jpg",
]

link_list = []

for filepath in filepath_list:
    response = client.files_upload(file=filepath)
    link = response.data['file']['permalink']
    link_list.append(link)
```

## 2. Slack에서 지원하는 markdown을 이용하여 link들을 모아줍니다.

Slack에서 [markdown](https://api.slack.com/methods/files.upload)을 사용하여 미리 가져온 link들을 하나의 메세지로 만듭니다.

```python
link_message = ""
for link in link_list:
    link_message += f"<{link}| >"
```

## 3. 원하는 채팅방에 해당 메세지를 전송합니다.

작성할 메세지 마지막에 미리 만들어둔 markdwon 메세지를 합쳐서 채널로 전송합니다.

이 때, 채널에 전송하는 API는 [chat.postMessage](https://api.slack.com/methods/chat.postMessage)입니다.

```python
message = "Just do it"
message += link_message
client.chat_postMessage(channel="just-do-it", text=message)
```

실행 결과는 다음과 같습니다.

![multiple files.png](/assets/img/slack/upload/mulitple-files.png)

## 예시 코드

```python
from slack import WebClient
client = WebClient(token="Your Token")

filepath_list = [
    "/home/ubuntu/just.jpg",
    "/home/ubuntu/do.jpg",
    "/home/ubuntu/it.jpg",
]

link_list = []

for filepath in filepath_list:
    response = client.files_upload(file=filepath)
    link = response.data['file']['permalink']
    link_list.append(link)

link_message = ""
for link in link_list:
    link_message += f"<{link}| >"

message = "Just do it"
message += link_message
client.chat_postMessage(channel="just-do-it", text=message)
```