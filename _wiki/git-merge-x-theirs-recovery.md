---
layout  : wiki
title   : 잘못된 브랜치에서 쌓인 커밋을 git merge -X theirs로 복구하기
summary : 엉뚱한 브랜치에서 한참을 작업하다 뒤늦게 발견했을 때, master-candidate 브랜치와 git merge -X theirs로 정리한 경험
date    : 2026-07-26 00:53:12 +0900
updated : 2026-07-26 00:53:12 +0900
tag     : git 브랜치 merge 트러블슈팅
toc     : true
public  : true
parent  : [[index]]
latex   : false
---
* TOC
{:toc}

## 사고 상황

작업을 하다가 브랜치를 잘못 짚은 채로 커밋을 계속 이어갔다. 문제는 한두 커밋 선에서 끝난 게 아니라, **꽤 많은 수의 커밋이 쌓인 뒤에야** 알아챘다는 점이다. 이 시점에는 이미 아래와 같은 상태였다.

- 원래 작업했어야 할 브랜치(예: `master`)는 그대로 뒤처져 있음
- 실제 작업 내용은 전부 엉뚱한 브랜치에 커밋으로 쌓여 있음
- 커밋 수가 많아서 `git cherry-pick`으로 하나씩 옮기기엔 비효율적이고 충돌 위험도 큼

되돌리거나(`reset`) 옮기는(`cherry-pick`) 방식보다, **"엉뚱한 브랜치의 최종 결과물을 통째로 올바른 브랜치 위에 얹는다"** 는 접근이 필요했다.

## 복구 절차

`master`를 직접 건드리기보다, 검증용 브랜치를 하나 만들어서 그 위에서 병합 결과를 먼저 확인하는 방식을 썼다.

```bash
# 1. 원래 브랜치(master) 기준으로 검증용 브랜치 생성
git checkout master
git checkout -b master-candidate

# 2. 잘못 쌓인 브랜치의 변경 내용을 충돌 시 무조건 우선 채택하며 병합
git merge -X theirs wrong-branch

# 3. 결과 검증 (빌드, 테스트, diff 확인 등)
git diff master master-candidate

# 4. 문제 없으면 master로 반영
git checkout master
git merge master-candidate
```

`master-candidate`라는 이름의 완충 브랜치를 둔 이유는, `-X theirs` 병합이 예상과 다르게 동작했을 때 `master`가 오염되지 않도록 하기 위해서다. 검증이 끝나기 전까지는 언제든 `master-candidate`만 버리고 다시 시작할 수 있다.

## `git merge -X theirs`가 정확히 하는 일

`-X theirs`는 `git merge`의 **병합 전략 옵션**이다. 여기서 이름이 헷갈리기 쉬운데, `merge` 상황에서 `theirs`는 **"지금 merge 명령어의 인자로 넘긴 브랜치"** 를 가리킨다. 즉 `git merge -X theirs wrong-branch`는 "충돌이 나는 부분은 무조건 `wrong-branch` 쪽 내용으로 채택한다"는 뜻이다.

핵심은 이게 **정상적으로 3-way merge를 수행하면서, 자동으로 해결하지 못하는 충돌 지점에 한해서만** `theirs` 쪽을 택한다는 점이다. 충돌이 없는 부분은 그대로 두 브랜치 내용이 정상적으로 합쳐진다. 그래서 "브랜치 하나를 통째로 덮어쓰는" 것과는 다르다.

### `git checkout --theirs` / `git merge-file --theirs`와의 차이

이름이 같아서 자주 혼동되는 세 가지를 구분해두면 좋다.

| 명령 | 동작 |
|---|---|
| `git merge -X theirs <branch>` | 정상적인 3-way merge를 수행하되, **자동 해결이 안 되는 충돌**만 상대 브랜치 내용으로 채움 |
| `git checkout --theirs <path>` | conflict 상태에서 **특정 파일 전체**를 상대 브랜치 버전으로 덮어씀 (merge 로직 없이 통째로 교체) |
| `git checkout <branch> -- <path>` | merge 여부와 무관하게 특정 브랜치의 특정 파일을 그대로 가져옴 |

이번 케이스에서는 커밋 수가 많고 충돌 지점이 산발적이었기 때문에, 파일 단위로 골라내는 `checkout --theirs`보다 병합 전체를 자동화할 수 있는 `merge -X theirs`가 더 적합했다.

## 재발 방지

사고의 원인은 결국 "지금 내가 어느 브랜치에 있는지 확인하지 않고 커밋을 계속 이어간 것"이었다. 다음번엔 아래를 습관화하기로 했다.

- **작업 시작 전 `git status` / `git branch`로 현재 브랜치 확인** — 특히 브랜치를 바꾼 직후, 또는 며칠 만에 다시 작업을 이어갈 때
- **셸 프롬프트에 현재 브랜치명 표시** (`git-prompt.sh`, oh-my-zsh 테마 등) — 눈에 안 보이면 결국 또 놓친다
- **커밋 전 `git status -s`로 변경 파일과 브랜치를 한 번 더 확인**하는 습관
- 저장소 설정이 허용한다면 **`master` 등 주요 브랜치에 branch protection**을 걸어 실수로 직접 커밋되는 걸 막기
- 브랜치 전환이 잦다면 `git worktree`로 브랜치별 작업 디렉토리를 물리적으로 분리하는 것도 고려할 만하다

## 정리

- 엉뚱한 브랜치에 커밋이 많이 쌓였을 때는 `cherry-pick`보다 `merge -X theirs`가 더 실용적일 수 있다.
- `master`를 직접 건드리지 않고 `master-candidate` 같은 완충 브랜치에서 먼저 검증하면 안전하다.
- `-X theirs`(merge 옵션)와 `--theirs`(checkout 옵션)는 이름은 비슷해도 동작 범위가 다르다는 걸 기억해두자.
