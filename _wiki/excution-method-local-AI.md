---
layout  : wiki
title   : 로컬 AI 실행 방법 (LM Studio로 Qwen3 · DeepSeek R1 돌리기)
summary : 맥미니 M4(16GB)에서 LM Studio로 로컬 LLM을 앱·터미널·API 세 가지 방법으로 실행하는 정리
date    : 2026-11-10 19:54:57 +0900
updated : 2026-11-10 19:54:57 +0900
tag     : lmstudio llm local-ai qwen deepseek mac
toc     : true
public  : true
parent  : [[index]]
latex   : false
---
* TOC
{:toc}

# 개요

Mac mini M4(메모리 16GB)에서 **LM Studio**로 로컬 LLM을 돌리는 방법을 정리했다.
인터넷 없이 내 컴퓨터 안에서만 동작하기 때문에 대화 내용이 외부로 나가지 않는다.

실행 방법은 세 가지다.

| 방법 | 언제 쓰나 |
|---|---|
| 1. LM Studio 앱(GUI) | 그냥 채팅하고 싶을 때. 가장 쉽다 |
| 2. 터미널 `lms` 명령 | SSH로 접속했을 때, 모델 로드/언로드를 빠르게 할 때 |
| 3. API 서버 (`localhost:1234`) | 스크립트·다른 프로그램에서 AI를 호출할 때 |

# 설치된 모델

| 모델 | 형식 | 크기 | 특징 |
|---|---|---|---|
| `qwen/qwen3-8b` | MLX 4bit | 4.62 GB | 가볍고 빠름. 평소 쓰기 좋음 |
| `deepseek/deepseek-r1-distill-qwen-14b` | GGUF Q4_K_M | 8.99 GB | 추론(생각) 모델. 수학·논리에 강하지만 느림 |

설치된 모델 목록은 언제든 터미널에서 확인할 수 있다.

```bash
lms ls
```

# 방법 1. LM Studio 앱에서 채팅

1. LM Studio 앱 실행
2. 왼쪽 사이드바에서 **Chat** 탭 선택
3. 화면 상단 **"Select a model to load"** 클릭 → 사용할 모델 선택
4. 로딩이 끝나면 바로 채팅

모델을 바꾸려면 같은 자리에서 다른 모델을 고르면 된다.

# 방법 2. 터미널에서 `lms` 명령으로 실행

`lms`는 LM Studio에 포함된 CLI 도구다. (`~/.lmstudio/bin/lms`)
명령을 못 찾는다고 나오면 아래처럼 PATH를 한 번 등록해 준다.

```bash
~/.lmstudio/bin/lms bootstrap
```

## 모델 올리기 / 확인 / 내리기

```bash
# 모델을 메모리에 올리기
lms load qwen/qwen3-8b
lms load deepseek/deepseek-r1-distill-qwen-14b

# 지금 메모리에 올라가 있는 모델 확인
lms ps

# 특정 모델 내리기 / 전부 내리기
lms unload qwen/qwen3-8b
lms unload --all
```

## 터미널에서 바로 대화

```bash
lms chat qwen/qwen3-8b
```

대화를 끝내려면 `Ctrl + C`.

# 방법 3. API 서버로 다른 프로그램에서 호출

LM Studio는 **OpenAI와 같은 형식의 API**를 제공한다.
그래서 OpenAI용으로 만들어진 도구·라이브러리를 주소만 바꿔서 그대로 쓸 수 있다.

## 서버 켜기 / 끄기

```bash
lms server start     # http://localhost:1234 에서 서버 시작
lms server status    # 상태 확인
lms server stop      # 서버 종료
```

## curl로 호출

```bash
curl http://localhost:1234/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen/qwen3-8b",
    "messages": [{"role": "user", "content": "안녕? 자기소개 해줘"}]
  }'
```

## Python으로 호출

`openai` 패키지를 그대로 쓰되 `base_url`만 로컬 주소로 바꾼다.
API 키는 확인하지 않으므로 아무 문자열이나 넣으면 된다.

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:1234/v1", api_key="lm-studio")

resp = client.chat.completions.create(
    model="qwen/qwen3-8b",
    messages=[{"role": "user", "content": "로컬 LLM의 장점을 3줄로 알려줘"}],
)
print(resp.choices[0].message.content)
```

> 다른 도구(예: 에디터 플러그인)에 연결할 때도
> **API 주소 = `http://localhost:1234/v1`**, **모델 이름 = `lms ls`에 나온 이름**만 넣으면 된다.

# 주의할 점

## 메모리: 두 모델을 동시에 띄우지 말 것

- 두 모델 합계 약 **13.6GB**인데 맥미니 메모리는 **16GB**다.
  macOS와 다른 앱이 쓰는 메모리까지 생각하면 동시에 올리면 스왑이 생겨 매우 느려지거나 멈출 수 있다.
- **한 번에 하나씩** 올려서 쓰고, 바꿀 때는 먼저 내린다.

```bash
lms unload --all && lms load deepseek/deepseek-r1-distill-qwen-14b
```

- 다 쓰고 나면 `lms unload --all`로 메모리를 비워두자.

## 어떤 모델을 쓸까

- **평소 질문, 요약, 번역** → `qwen3-8b` (빠름)
- **수학, 논리, 단계적 추론이 필요한 문제** → `deepseek-r1-distill-qwen-14b`
  - 답하기 전에 `<think> ... </think>` 안에 생각 과정을 길게 출력한다.
    그래서 답이 늦게 나오는 것이 정상이다.

# 명령어 요약

| 하고 싶은 것 | 명령 |
|---|---|
| 설치된 모델 보기 | `lms ls` |
| 모델 올리기 | `lms load <모델이름>` |
| 올라간 모델 보기 | `lms ps` |
| 모델 전부 내리기 | `lms unload --all` |
| 터미널 채팅 | `lms chat <모델이름>` |
| API 서버 켜기/끄기 | `lms server start` / `lms server stop` |
