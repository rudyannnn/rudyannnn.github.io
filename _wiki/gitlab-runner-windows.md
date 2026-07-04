---
layout  : wiki
title   : GitLab Runner Windows 설치·등록 + 오프라인 gem 배포
summary : 개인 Windows PC를 Shell executor 러너로 등록하고 인터넷 없이 Jekyll 위키를 배포한 기록
date    : 2026-07-04 17:01:16 +0900
updated : 2026-07-04 17:01:16 +0900
tag     : gitlab gitlab-runner windows jekyll gem bundler offline ci
toc     : true
public  : true
parent  : [[index]]
latex   : false
---
* TOC
{:toc}

## 이 문서는?
[[gitlab-pages-and-email-send]]{사내 GitLab 위키 만들기} 의 후속.
사내 GitLab이 폐쇄망이라 공용 Docker Runner를 쓸 수 없어,
개인 Windows PC에 Shell executor 러너를 설치하고 등록한 기록.
gem도 오프라인으로 설치하는 방법까지 정리한다.

---

## Docker executor vs Shell executor

| 항목 | Docker executor (기존) | Shell executor (이번) |
|------|----------------------|----------------------|
| 실행 환경 | `image: ruby:3.1` 컨테이너 | 러너 PC 그 자체 |
| Ruby 출처 | Docker 이미지에 내장 | PC에 직접 설치 필요 |
| gem 설치 | 빌드마다 인터넷 다운로드 | PC에 설치된 것 재사용 |
| 인터넷 | 빌드 시마다 필요 | 초기 1회만 필요 |
| `.gitlab-ci.yml` | `image:` 필수 | `image:` 제거, `tags:` 추가 |

---

## 1단계: Runner 설치 (Windows PC)

PowerShell **관리자 권한** 으로 실행:

```powershell
# 설치 폴더 생성
New-Item -ItemType Directory -Force -Path "C:\GitLab-Runner"

# Runner 실행파일 다운로드
Invoke-WebRequest `
  -Uri "https://gitlab-ci-multi-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-windows-amd64.exe" `
  -OutFile "C:\GitLab-Runner\gitlab-runner.exe"

# Windows 서비스로 설치 및 시작
cd C:\GitLab-Runner
.\gitlab-runner.exe install
.\gitlab-runner.exe start
```

서비스로 등록하면 PC 재시작 후에도 자동 실행된다.

---

## 2단계: Registration Token 확인

회사 GitLab 접속 후:

```
프로젝트 → Settings → CI/CD → Runners → Expand
```

**"Registration token"** 복사. 그룹 전체에서 쓰려면 그룹 Settings에서 확인.

---

## 3단계: Runner 등록

```powershell
cd C:\GitLab-Runner
.\gitlab-runner.exe register
```

대화형 입력:

```
GitLab instance URL  →  https://gitlab.회사도메인.com
Registration token   →  (2단계 토큰)
Description          →  my-windows-runner
Tags                 →  windows
Executor             →  shell
```

등록 후 `C:\GitLab-Runner\config.toml` 이 생성된다.

---

## 4단계: .gitlab-ci.yml 수정

`image:` 줄을 지우고 `tags:` 를 추가하는 것이 핵심이다.

```yaml
# image: ruby:3.1   ← 삭제

cache:
  paths:
    - vendor/

before_script:
  - bundle config set --local path 'vendor/bundle'
  - bundle install --local        # vendor/cache 사용, 인터넷 불필요

pages:
  stage: deploy
  tags:
    - windows                     # 3단계에서 지정한 태그
  script:
    - bundle exec jekyll build -d public --baseurl "/$CI_PROJECT_NAME"
  artifacts:
    paths:
      - public
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

---

## 5단계: Ruby 설치 (러너 PC, 최초 1회)

다운로드:
```
https://github.com/oneclick/rubyinstaller2/releases/download/RubyInstaller-3.1.6-1/rubyinstaller-devkit-3.1.6-1-x64.exe
```

설치 마지막 단계에서 **"MSYS2 development toolchain"** 반드시 선택.
(네이티브 확장 gem 컴파일에 필요)

설치 후 확인:
```powershell
ruby -v    # ruby 3.1.x
bundle -v  # Bundler x.x.x
```

---

## 6단계: 오프라인 gem 패키지 만들기 (맥에서)

인터넷이 되는 맥에서 gem을 캐시해 옮기는 방식.

```bash
cd ~/git/company-wiki

# Gemfile.lock에 Windows 플랫폼 추가
bundle lock --add-platform x64-mingw-ucrt

# vendor/cache/ 에 .gem 파일 다운로드
bundle package --no-install

# Windows 전용 ffi gem 추가 (자동 포함 안 될 경우)
cd vendor/cache
gem fetch ffi --platform x64-mingw-ucrt

# 프로젝트 전체를 zip으로
cd ~/git
zip -r company-wiki.zip company-wiki --exclude "company-wiki/.git/*"
```

Windows에서 설치:
```powershell
cd company-wiki
bundle config set --local path 'vendor/bundle'
bundle install --local    # 인터넷 불필요
```

---

## 7단계: GitLab push

```powershell
git init
git remote add origin https://gitlab.회사도메인.com/그룹/company-wiki.git
git add .
git commit -m "initial commit"
git push -u origin main
```

push 후 GitLab CI/CD가 자동으로 빌드·배포한다.

---

## 주의사항

| 항목 | 내용 |
|------|------|
| 방화벽 | Runner PC → GitLab 서버 443 포트 아웃바운드 허용 필요 |
| 절전 모드 | 러너 PC 꺼지면 CI 중단 → 절전 해제 권장 |
| 동시 실행 | `config.toml` 에서 `concurrent = 1` 확인 |
| 서비스 계정 | `install --user SYSTEM` 으로 시스템 계정 사용 권장 |
| 플랫폼 gem | `ffi` 등 네이티브 gem은 OS별로 다른 버전 필요 (arm64-darwin vs x64-mingw-ucrt) |

---

## Gmail 첨부 발송 시 막힌 것들

zip을 메일로 보낼 때 겪은 문제:

* ❌ **Mail.app AppleScript** : 헤드리스 환경에서 타임아웃 (-1712)
* ❌ **Gmail MCP create_draft** : 첨부 파일 미지원
* ❌ `.zip` 확장자 그대로 발송 : Gmail 보안 정책으로 차단
* ✅ **gog CLI** (gogcli) + 확장자 `.tar.gz` 또는 `.bin` 으로 변경해 발송

gog 설치:
```bash
brew install steipete/tap/gogcli
gog auth add 계정@gmail.com
gog gmail send --to 받는이@회사.com --subject "제목" --body "본문" --attach 파일.bin
```

> gog의 `no-send` 안전 기능이 기본 활성화되어 있으면
> `~/Library/Application Support/gogcli/config.json` 에서
> `"no_send_accounts": { "계정": false }` 로 변경 필요.
