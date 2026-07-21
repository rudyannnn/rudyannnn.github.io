---
layout  : wiki
title   : Windows에서 여러 Python 버전 중 실행할 것 선택하기
summary : conda, Python Install Manager, Microsoft Store 스텁이 뒤섞인 Windows에서 원하는 python.exe를 고르는 법
date    : 2026-07-21 10:59:04 +0900
updated : 2026-07-21 10:59:04 +0900
tag     : python windows conda powershell py-launcher
toc     : true
public  : true
parent  : [[index]]
latex   : false
---
* TOC
{:toc}

## 왜 필요한가

Windows에서 개발을 좀 하다 보면 Python이 한 대의 PC에 여러 버전, 여러 경로로 깔리기 쉽다.

* conda/miniforge (base 환경)
* python.org 개별 설치, 또는 최신 **Python Install Manager**(`pythoncore-3.14-64` 같은 폴더 구조)
* Microsoft Store의 `python.exe` 앱 실행 별칭(stub)

이 상태에서 그냥 `python`이라고 치면, **어느 게 실행될지는 PATH 순서와 쉘(cmd/PowerShell) 설정에 따라 달라진다.** 실제로 겪은 사례: cmd에서는 conda의 base 환경이 자동 활성화되어 있어 `python`이 miniforge의 3.13(패키지 미설치 상태)을 가리켰고, PowerShell에서는 conda가 자동 활성화되지 않아 `python`이 pip으로 패키지를 다 설치해둔 3.14를 가리켰다. 그 결과 **PowerShell에선 멀쩡히 되던 PySide6 GUI가 cmd에서는 `DLL load failed while importing QtCore`로 죽었다.** VC++ 재배포 패키지, 64비트 여부, DLL 파일 존재까지 다 확인했지만 원인은 그게 아니라 **애초에 다른 인터프리터를 실행하고 있었던 것**이었다.

즉, "설치는 됐는데 실행이 안 된다"는 에러의 상당수는 파이썬 자체 문제가 아니라 **어떤 python.exe가 실행되는지 모르고 있어서** 생긴다. 아래는 그걸 확인하고 원하는 대로 고정하는 방법들이다.

## 1. 지금 어떤 python이 실행되는지 확인하기

```
# cmd
where python

# PowerShell
Get-Command python -All | Select-Object Source
```

여러 줄이 나오면 **첫 줄(PATH에서 가장 먼저 찾은 것)이 실제로 실행되는 것**이다. cmd와 PowerShell에서 결과가 다르게 나올 수 있다 — 서로 다른 프로필/자동 활성화 설정 때문이다.

## 2. `py` 런처로 버전을 명시해서 실행 (제일 확실함)

최신 Python Install Manager(또는 예전 py launcher)가 깔려 있다면 `py` 명령으로 설치된 버전 목록과 경로를 볼 수 있다.

```
py -0p                     # 설치된 버전과 경로 전체 목록
py -3.14 vcom_gui.py        # 3.14로 스크립트 실행
py -3.14 -m pip install -r requirements.txt   # 3.14의 pip으로 설치
```

`python` 대신 항상 `py -3.14`처럼 버전을 붙이면, PATH가 뭘 가리키든 상관없이 원하는 버전이 확실하게 실행된다.

## 3. conda(miniforge/anaconda) base 자동 활성화가 방해될 때

프롬프트 앞에 `(base)`가 붙어 있다면 conda의 base 환경이 켜져 있는 것이고, 그동안은 `python`이 conda 쪽을 가리킨다.

```
conda deactivate                          # 그때그때 끄기
conda config --set auto_activate_base false   # 아예 자동 활성화 끄기 (추천)
```

두 번째로 한 번 설정해두면 이후 새 터미널을 열어도 base가 자동으로 켜지지 않는다. conda 환경이 필요할 때만 `conda activate base`로 수동 전환하면 된다.

## 4. PowerShell에서 `python`을 특정 버전으로 영구 고정

자주 쓰는 버전이 있다면 PowerShell 프로필에 별칭을 걸어두는 게 제일 편하다.

```powershell
notepad $PROFILE
```

(파일이 없으면 만들지 물어보는데 예 선택) 아래 줄 추가:

```powershell
Set-Alias python "C:\Users\Administrator\AppData\Local\Python\pythoncore-3.14-64\python.exe"
```

저장 후 PowerShell을 새로 열면, 이후로는 그냥 `python`만 쳐도 항상 이 버전이 실행된다. 경로는 `py -0p`로 확인한 걸 그대로 쓰면 된다.

## 5. 그냥 전체 경로로 직접 실행

가장 원시적이지만 가장 확실한 방법. 스크립트나 배치파일에 박아둘 때 유용하다.

```powershell
C:\Users\Administrator\AppData\Local\Python\pythoncore-3.14-64\python.exe vcom_gui.py
```

## 덤: 새로 설치한 Python에 pip이 없을 때

Python Install Manager로 새로 깐 버전은 pip이 기본 포함되어 있지 않을 수 있다.

```
python -m ensurepip --upgrade
python -m pip install --upgrade pip
```

로 부트스트랩하면 이후 `pip install`이 정상 동작한다.

## 정리

| 상황 | 방법 |
|---|---|
| 지금 뭐가 실행되는지만 확인하고 싶다 | `where python` / `Get-Command python -All` |
| 한 번만 특정 버전으로 실행하고 싶다 | `py -3.14 script.py` |
| conda base가 자꾸 끼어든다 | `conda config --set auto_activate_base false` |
| PowerShell에서 특정 버전을 기본으로 쓰고 싶다 | `$PROFILE`에 `Set-Alias python <경로>` |
| 확실하게 그 버전만 쓰고 싶다 | 전체 경로로 직접 실행 |

"설치는 됐는데 import/실행이 안 된다"는 에러를 만나면, DLL·백신·방화벽을 의심하기 전에 먼저 `pip install`한 파이썬과 스크립트를 실행하는 파이썬이 같은 인터프리터인지부터 확인하자.
