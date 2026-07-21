---
layout  : wiki
title   : Windows PySide6 "DLL load failed while importing QtCore" 트러블슈팅
summary : VC++/DLL/백신 의심하기 전에 cmd와 PowerShell이 서로 다른 python.exe를 가리키고 있는지부터 확인하자
date    : 2026-07-21 10:59:04 +0900
updated : 2026-07-21 10:59:04 +0900
tag     : python windows pyside6 conda powershell troubleshooting
toc     : true
public  : true
parent  : [[index]]
latex   : false
---
* TOC
{:toc}

## 상황

VCOM 플리커 최적화용 PySide6 GUI(`vcom_gui.py`)를 Windows 노트북에서 실행하려는데 다음 에러가 났다.

```
ImportError: DLL load failed while importing QtCore
```

`pip install -r requirements.txt`는 에러 없이 끝났고, `from PySide6 import QtCore`만 하면 이 에러가 났다.

## 처음에 의심한 것들 (다 정상이었음)

1. **VC++ 재배포 패키지(x64) 설치 여부** → 이미 설치되어 있었음
2. **Python이 64비트인지** → 64비트 맞음
3. **`Qt6Core.dll` 파일이 실제로 있는지** (회사 백신이 격리했을 가능성) → 파일 정상 존재

셋 다 정상인데도 에러가 나서, 다음으로 "회사 보안 정책(AppLocker/EDR)이 DLL 로딩을 막고 있나?"를 의심했다. 노트북을 들고 다니며 집에서도 재현되는지 확인했는데 — **cmd에서는 여전히 안 되고, PowerShell에서는 바로 됐다.**

## 진짜 원인: cmd와 PowerShell이 서로 다른 python.exe를 실행하고 있었다

```
(base) D:\...> where python
C:\ProgramData\miniforge3\python.exe
C:\Users\Administrator\AppData\Local\Microsoft\WindowsApps\python.exe
C:\Users\Administrator\AppData\Local\Python\bin\python.exe
```

cmd 프롬프트 앞에 `(base)`가 붙어 있던 게 단서였다. **miniforge(conda)의 base 환경이 cmd에서 자동으로 활성화**되어 있어서, cmd의 `python`은 `C:\ProgramData\miniforge3\python.exe`(3.13, PySide6 미설치)를 가리키고 있었다.

반면 PowerShell은 conda base가 자동 활성화되지 않아서, `python`이 Python Install Manager로 설치한 `C:\Users\...\AppData\Local\Python\pythoncore-3.14-64\python.exe`(3.14, PySide6가 제대로 설치된 쪽)를 가리켰다.

즉 **`pip install`한 파이썬과 스크립트를 실행한 파이썬이 서로 다른 인터프리터**였던 것 — 회사 네트워크나 보안정책과는 무관한 문제였다.

## 확인 방법

```powershell
# cmd
where python

# PowerShell
Get-Command python | Select-Object Source
```

두 결과의 경로가 다르면 바로 이 문제다.

## 해결책

### 1. 그때그때 conda base 끄기
```
conda deactivate
```

### 2. cmd에서 conda base 자동 활성화를 아예 꺼버리기 (추천)
```
conda config --set auto_activate_base false
```
한 번만 실행하면 이후 cmd를 열어도 base가 자동으로 안 켜진다. conda 환경이 필요할 때만 `conda activate base`로 수동 전환.

### 3. 여러 버전이 설치되어 있을 때 `py` 런처로 명시
```
py -0p                    # 설치된 버전과 경로 목록
py -3.14 vcom_gui.py       # 버전을 지정해서 실행
py -3.14 -m pip install -r requirements.txt
```

### 4. PowerShell 프로필에서 python을 특정 버전으로 영구 고정
```powershell
notepad $PROFILE
```
파일에 아래 줄 추가:
```powershell
Set-Alias python "C:\Users\Administrator\AppData\Local\Python\pythoncore-3.14-64\python.exe"
```
저장 후 PowerShell을 새로 열면 이후 `python`은 항상 이 3.14를 가리킨다.

### 5. 그냥 전체 경로로 직접 실행
```powershell
C:\Users\Administrator\AppData\Local\Python\pythoncore-3.14-64\python.exe vcom_gui.py
```

## 덤: pip이 아예 없는 경우

이번에 새로 설치한 3.14는 Python Install Manager로 깐 것이라 pip이 기본 포함되어 있지 않았다. 이럴 때는:
```
python -m ensurepip --upgrade
python -m pip install --upgrade pip
```
로 부트스트랩한 다음 평소처럼 `pip install`하면 된다.

## 교훈

**"설치는 됐는데 import가 안 된다"** 는 에러를 만나면, DLL/백신/방화벽/재배포 패키지를 의심하기 전에 먼저

> `pip install`한 파이썬과 스크립트를 실행하는 파이썬이 같은 인터프리터인가?

를 `where python`(cmd) / `Get-Command python`(PowerShell)으로 확인하는 게 우선이다. conda, 여러 Python 버전, Microsoft Store 스텁 alias 등이 섞여 있는 Windows 환경에서는 이게 원인일 확률이 생각보다 높다.
