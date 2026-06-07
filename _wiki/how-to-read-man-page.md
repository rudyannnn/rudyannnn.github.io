---
layout  : wiki
title   : man 페이지 읽는 법
summary : man tmux를 예시로 각 섹션을 어떻게 읽으면 되는지 정리
date    : 2026-06-07 11:16:58 +0900
updated : 2026-06-07 11:16:58 +0900
tag     : bash man terminal linux tool
toc     : true
public  : true
parent  : [[index]]
latex   : false
---
* TOC
{:toc}

## man 페이지란

`man`(manual)은 유닉스 계열 시스템의 내장 레퍼런스 문서다.
인터넷 검색 없이도 명령어의 모든 옵션과 동작을 확인할 수 있다.

```bash
man tmux        # tmux 매뉴얼 열기
man man         # man 자체의 매뉴얼
```

---

## 기본 조작 (less 기반)

man 페이지는 `less` 뷰어로 열린다.

| 키 | 동작 |
|----|------|
| `j` / `k` | 한 줄 아래 / 위 |
| `f` / `b` | 한 화면 앞 / 뒤 |
| `g` / `G` | 문서 맨 앞 / 맨 끝 |
| `/검색어` | 앞 방향 검색 |
| `?검색어` | 뒤 방향 검색 |
| `n` / `N` | 다음 / 이전 검색 결과 |
| `q` | 종료 |

---

## 섹션 번호란

`TMUX(1)` 처럼 이름 뒤의 숫자가 **섹션 번호**다.

| 번호 | 의미 | 예시 |
|------|------|------|
| 1 | 일반 사용자 명령 | `ls(1)`, `tmux(1)` |
| 2 | 시스템 콜 | `fork(2)`, `open(2)` |
| 3 | C 라이브러리 함수 | `printf(3)`, `malloc(3)` |
| 5 | 파일 형식 / 설정 파일 | `crontab(5)`, `passwd(5)` |
| 8 | 시스템 관리 명령 | `sudo(8)`, `nginx(8)` |

같은 이름이 여러 섹션에 있을 때 섹션을 지정해서 열 수 있다:

```bash
man 3 printf    # C 라이브러리 printf
man 1 printf    # 쉘 명령 printf
```

---

## 각 섹션 읽는 법 — `man tmux` 예시

아래 표는 실제 man 페이지(왼쪽)와 읽는 방법(오른쪽)을 나란히 정리한 것이다.

### 헤더 줄

| man 페이지 | 읽는 법 |
|-----------|---------|
| `TMUX(1)  General Commands Manual  TMUX(1)` | 맨 위 한 줄. 왼쪽·오른쪽은 `명령(섹션)`, 가운데는 매뉴얼 분류명. 지금 보고 있는 섹션 번호를 빠르게 확인할 때 유용. |

---

### NAME

```
NAME
     tmux – terminal multiplexer
```

| man 페이지 | 읽는 법 |
|-----------|---------|
| 명령 이름과 한 줄 설명 | 이 명령이 무엇인지 한 문장으로 요약. `apropos` / `man -k` 검색 결과에 바로 이 문장이 표시된다. 처음 보는 명령어라면 여기서 "쓸 도구인지 아닌지"를 판단한다. |

---

### SYNOPSIS

```
SYNOPSIS
     tmux [-2CDhlNuVv] [-c shell-command] [-f file]
          [-L socket-name] [-S socket-path] [-T features]
          [command [flags]]
```

이 섹션이 **가장 중요**하다. 명령 사용법 전체가 한눈에 보인다.

| 표기 | 의미 |
|------|------|
| `[-2CDhlNuVv]` | `-2`, `-C`, `-D` … 중 원하는 것을 선택적으로 사용. 대괄호 `[]` = 생략 가능 |
| `-c shell-command` | `-c` 뒤에 반드시 값이 와야 함. 이탤릭(`_shell-command_`)은 사용자가 채울 자리 |
| `[command [flags]]` | 전체가 생략 가능한 위치 인자. 중첩 대괄호는 "flags도 선택적"이라는 뜻 |
| `<value>` | 꺾쇠(`<>`)는 필수 인자 (man마다 표기 방식이 다를 수 있음) |

```bash
# SYNOPSIS를 보고 바로 작성할 수 있는 예시
tmux -f ~/.config/tmux/tmux.conf new-session
#    ↑ [-f file]                  ↑ [command [flags]]
```

---

### DESCRIPTION

```
DESCRIPTION
     tmux is a terminal multiplexer: it enables a number of
     terminals to be created, accessed, and controlled from a
     single screen. tmux may be detached from a screen and
     continue running in the background, then later reattached.
     ...
```

| man 페이지 | 읽는 법 |
|-----------|---------|
| 명령 전체 동작 원리 서술 | 처음 접하는 도구라면 통독. 익숙한 도구라면 **옵션·하위 명령을 찾기 전** 개념 확인용으로 필요할 때만 읽는다. 길이가 긴 경우가 많으니 `/` 검색으로 키워드를 찾는 게 효율적. |

---

### OPTIONS

```
     -2            Force tmux to assume the terminal supports
                   256 colours.

     -f file       Specify an alternative configuration file.
                   By default, tmux loads the system
                   configuration file from /opt/homebrew/etc/
                   tmux.conf ...
```

| man 페이지 | 읽는 법 |
|-----------|---------|
| 각 플래그의 상세 설명 | SYNOPSIS에서 본 `[-f file]`의 실제 동작을 여기서 확인. 옵션이 많다면 `/` 로 `-f`를 검색해서 바로 이동. 값이 필요한 옵션은 `default:` 혹은 `By default` 문장을 찾아 기본값 파악. |

---

### COMMANDS (하위 명령 섹션)

tmux처럼 하위 명령이 많은 도구는 별도 섹션으로 분리된다.

```
COMMANDS
     ...
     new-session [-AdDEPX] [-c start-directory] [-e environment]
                 [-f flags] [-F format] [-n window-name]
                 [-s session-name] [-t group-name] [-x width]
                 [-y height] [shell-command]
               (alias: new)
     ...
```

| man 페이지 | 읽는 법 |
|-----------|---------|
| 하위 명령별 SYNOPSIS | 최상위 SYNOPSIS와 읽는 법이 동일. `(alias: new)` 처럼 축약명도 함께 적혀 있다. 원하는 하위 명령은 `/new-session` 으로 바로 검색. |

---

### SEE ALSO

```
SEE ALSO
     pty(4), tmux-server(8), ...
```

| man 페이지 | 읽는 법 |
|-----------|---------|
| 연관 문서 목록 | `pty(4)` → `man 4 pty` 로 바로 열 수 있다. 현재 명령과 함께 쓰이는 도구, 또는 개념의 원 출처를 찾을 때 유용. |

---

### AUTHORS / HISTORY / BUGS

| 섹션 | 읽는 법 |
|------|---------|
| AUTHORS | 원 저자. 보통 읽을 일 없음 |
| HISTORY | 언제 처음 등장했는지. 버전 의존 기능 파악에 도움 |
| BUGS | **꼭 확인**. 알려진 동작 제한이나 예외가 적혀 있다 |

---

## 실전 팁

### 키워드로 바로 이동

```bash
# man 안에서 / 로 검색
/split-window        # pane 분할 명령 바로 찾기
/ENVIRONMENT         # 환경변수 섹션으로 점프
```

### 특정 섹션만 검색

```bash
man -k split         # "split"이 NAME에 포함된 모든 man 항목 나열
man -K split-window  # 전체 man DB에서 본문까지 검색 (느림)
```

### tldr — 빠른 요약이 필요할 때

`man`이 너무 길면 `tldr` 로 핵심 예시만 볼 수 있다.

```bash
brew install tldr
tldr tmux
```

`tldr`은 커뮤니티가 만든 요약이므로 정확한 레퍼런스는 항상 `man`을 우선한다.

---

## 관련 글

- [[tmux-layout]]{tmux 창 정렬하는 법}
