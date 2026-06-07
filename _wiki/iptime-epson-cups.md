---
layout  : wiki
title   : ipTIME 공유기 + Epson L3106 Mac 프린터 설정
summary : Apple Silicon Mac에서 ipTIME USB 프린트 서버로 Epson 설정하는 법
date    : 2026-06-07 14:59:52 +0900
updated : 2026-06-07 14:59:52 +0900
tag     : mac cups epson iptime printer gutenprint
toc     : true
public  : true
parent  : [[index]]
latex   : false
---
* TOC
{:toc}

## 환경

- 공유기: ipTIME (192.168.3.1), CUPS 2.3.3 내장
- 프린터: Epson L3106 (USB로 공유기에 연결)
- Mac: Apple Silicon (Mac mini), macOS

---

## 1. ipTIME 공유기 설정

관리자 페이지(`http://192.168.3.1`) 접속 후:

> **고급 설정 → USB 장치 → USB 프린트 서버** 활성화

활성화하면 CUPS 큐가 자동 생성된다.

- 큐 이름: `ipTIME_Printer`
- IPP URI: `ipp://192.168.3.1:631/printers/ipTIME_Printer`

### 큐 이름 확인 방법

USB 프린트 서버 활성화 후 아래 파이썬 스크립트로 실제 큐 이름을 확인할 수 있다.

```python
import socket, struct

data = b'\x01\x01\x40\x02\x00\x00\x00\x01\x01'
data += b'\x47' + struct.pack('>H', 18) + b'attributes-charset' + struct.pack('>H', 5) + b'utf-8'
data += b'\x48' + struct.pack('>H', 27) + b'attributes-natural-language' + struct.pack('>H', 5) + b'en-us'
data += b'\x03'
http = f"POST / HTTP/1.1\r\nHost: 192.168.3.1:631\r\nContent-Type: application/ipp\r\nContent-Length: {len(data)}\r\nConnection: close\r\n\r\n"

s = socket.socket()
s.settimeout(5)
s.connect(('192.168.3.1', 631))
s.send(http.encode() + data)
resp = b''
while True:
    c = s.recv(8192)
    if not c: break
    resp += c
s.close()

# 응답 파싱해서 printer-name 확인
idx = resp.find(b'\r\n\r\n')
ipp = resp[idx+4:] if idx >= 0 else resp
i = 8
while i < len(ipp):
    tag = ipp[i]; i += 1
    if tag == 0x03: break
    if tag in (0x01,0x02,0x04,0x05): continue
    nlen = struct.unpack('>H',ipp[i:i+2])[0]; i+=2
    name = ipp[i:i+nlen].decode('utf-8','replace'); i+=nlen
    vlen = struct.unpack('>H',ipp[i:i+2])[0]; i+=2
    val = ipp[i:i+vlen].decode('utf-8','replace'); i+=vlen
    if name: print(f"{name} = {val!r}")
```

---

## 2. Epson 기본 드라이버가 안 되는 이유

macOS에 설치된 Epson 드라이버(`rastertoescpII`)는 **USB 직접 통신**이 필요하다.

- Epson의 CIOSupport 프레임워크가 USB로 프린터와 양방향 통신(잉크 상태, 프린터 상태 등)
- 네트워크(IPP)로 연결하면 `com.epson.COM_001-error` 발생 → 인쇄 불가
- 내부적으로 SIGTSTP 신호로 흐름 제어해서 wrapper로 우회도 불가

---

## 3. 해결책: Gutenprint + Rosetta 2

Gutenprint는 오픈소스 프린터 드라이버로 네트워크 인쇄를 지원한다.
단, Homebrew cask로 배포되는 버전이 Intel(x86_64) 전용이라 Apple Silicon에서는 Rosetta 2가 필요하다.

### 설치

```bash
# 1. Gutenprint 설치 (sudo 필요)
brew install gutenprint

# 2. Rosetta 2 설치 (Apple Silicon에서 Intel 바이너리 실행)
sudo softwareupdate --install-rosetta --agree-to-license
```

### 프린터 등록

```bash
# L3106은 Gutenprint에 없으므로 동일 엔진의 L310 드라이버 사용
lpadmin -p EPSON_L3106 \
  -v "ipp://192.168.3.1:631/printers/ipTIME_Printer" \
  -m "gutenprint.5.3://escp2-l310/expert" \
  -D "EPSON L3106 (ipTIME)" -E

# 기본 프린터 설정
lpoptions -d EPSON_L3106
```

### 왜 L310 드라이버인가

Gutenprint 5.3.3 기준 L3100/L3106 드라이버가 없다.
L310과 L3106은 동일한 **ESC/P-R 엔진**을 사용하므로 호환된다.

### 테스트

```bash
lp -d EPSON_L3106 -o media=A4 /usr/share/cups/data/testprint
```

---

## 요약 체크리스트

1. [ ] ipTIME 관리자 → USB 프린트 서버 활성화
2. [ ] `brew install gutenprint`
3. [ ] `sudo softwareupdate --install-rosetta --agree-to-license`
4. [ ] `lpadmin` 으로 프린터 등록 (드라이버: `escp2-l310`)
5. [ ] 테스트 인쇄 확인
