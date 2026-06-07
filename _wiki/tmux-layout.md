---
layout  : wiki
title   : tmux 창 정렬하는 법
summary : pane 분할·크기 조정·레이아웃 프리셋 정리
date    : 2026-06-07 11:16:58 +0900
updated : 2026-06-07 11:16:58 +0900
tag     : tmux terminal linux tool
toc     : true
public  : true
parent  : [[index]]
latex   : false
---
* TOC
{:toc}

## 용어 정리

| 용어 | 설명 |
|------|------|
| **session** | tmux 실행 단위. 여러 window를 포함 |
| **window** | 탭처럼 전환되는 단위 |
| **pane** | window 안에서 분할된 하나의 화면 |

기본 prefix 키는 `Ctrl-b` (`C-b`).

---

## pane 분할

| 명령 | 단축키 | 설명 |
|------|--------|------|
| `C-b %` | — | 좌우(수직선) 분할 |
| `C-b "` | — | 위아래(수평선) 분할 |

```bash
# 명령줄에서 직접 분할
tmux split-window -h   # 좌우 분할
tmux split-window -v   # 위아래 분할
```

---

## pane 이동

| 단축키 | 설명 |
|--------|------|
| `C-b 방향키` | 인접 pane으로 이동 |
| `C-b o` | 다음 pane으로 순환 |
| `C-b q` → 번호 | 번호로 pane 직접 이동 |
| `C-b ;` | 직전 pane으로 돌아가기 |

---

## pane 크기 조정

| 단축키 | 설명 |
|--------|------|
| `C-b :resize-pane -L <n>` | 왼쪽으로 n칸 줄이기 |
| `C-b :resize-pane -R <n>` | 오른쪽으로 n칸 늘리기 |
| `C-b :resize-pane -U <n>` | 위로 n칸 늘리기 |
| `C-b :resize-pane -D <n>` | 아래로 n칸 줄이기 |
| `C-b Alt-방향키` | 방향키로 5칸씩 조정 |
| `C-b Ctrl-방향키` | 방향키로 1칸씩 조정 |

```bash
# 명령줄 예시 — 현재 pane을 오른쪽으로 10칸 확장
tmux resize-pane -R 10
```

---

## 레이아웃 프리셋 (자동 정렬)

`C-b Space` 를 누를 때마다 아래 5가지 프리셋을 **순환**한다.

| 이름 | 모습 | 설명 |
|------|------|------|
| `even-horizontal` | `\|A\|B\|C\|` | 좌우 균등 분할 |
| `even-vertical` | A/B/C (위→아래) | 위아래 균등 분할 |
| `main-horizontal` | 상단 큰 pane + 하단 여러 pane | 주 작업 + 보조 |
| `main-vertical` | 좌측 큰 pane + 우측 여러 pane | 주 작업 + 보조 |
| `tiled` | 격자 균등 배치 | 모든 pane 동일 크기 |

특정 레이아웃을 바로 적용하려면:

```bash
tmux select-layout even-horizontal
tmux select-layout even-vertical
tmux select-layout main-horizontal
tmux select-layout main-vertical
tmux select-layout tiled
```

---

## pane 최대화 / 복원

```
C-b z
```

현재 pane을 window 전체로 확대하고, 다시 누르면 원래 크기로 복원.

---

## pane 위치 바꾸기

| 단축키 | 설명 |
|--------|------|
| `C-b {` | 현재 pane을 앞쪽으로 이동 |
| `C-b }` | 현재 pane을 뒤쪽으로 이동 |
| `C-b C-o` | 모든 pane 위치를 한 칸씩 순환 |

---

## pane → 새 window로 분리

```bash
C-b !
# 또는
tmux break-pane
```

현재 pane을 독립된 window로 꺼낸다.

---

## 다른 window로 pane 이동

```bash
# 현재 pane을 window 번호 2로 이동
tmux move-pane -t :2
```

---

## window 관리

| 단축키 | 설명 |
|--------|------|
| `C-b c` | 새 window 생성 |
| `C-b n` / `C-b p` | 다음 / 이전 window |
| `C-b <숫자>` | 번호로 window 이동 |
| `C-b ,` | window 이름 변경 |
| `C-b &` | 현재 window 닫기 |
| `C-b w` | window 목록 보기 |

---

## 자주 쓰는 조합 예시

```bash
# 터미널을 3분할로 세팅 (좌/우상/우하)
tmux split-window -h          # 좌우 분할
tmux split-window -v          # 오른쪽 pane을 다시 위아래 분할
tmux select-layout main-vertical  # 좌측 메인, 우측 2분할 정렬
```
