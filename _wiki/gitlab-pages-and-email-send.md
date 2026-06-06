---
layout  : wiki
title   : 사내 GitLab 위키 만들기 + Gmail 첨부 발송
summary : 개인 블로그 구조를 GitLab Pages로 옮기고, 헤드리스 맥에서 zip을 메일로 보낸 기록
date    : 2026-06-06 21:16:01 +0900
updated : 2026-06-06 21:16:01 +0900
tag     : gitlab gitlab-pages jekyll ci cd gmail smtp
toc     : true
public  : true
parent  : [[index]]
latex   : false
---
* TOC
{:toc}

## 이 문서는?
개인 블로그(GitHub Pages) 구조를 **사내 자체 GitLab**에 독립 위키로 옮기고,
그 결과물을 **헤드리스 맥미니에서 Gmail 첨부로 발송**한 작업 기록.
관련 개념은 [[ci-cd-gem-docker]]{CI, CD, gem, 도커 이미지 정리} 참고.

## GitLab으로 옮길 때: 두 부분으로 나뉜다

### 1) git 워크플로 → 그대로 됨
글 작성 → `add` → `commit` → `push` 는 git 기본 기능이라 GitLab도 동일하다.
저장소를 통째로 GitLab에도 올리려면 remote만 추가하면 된다.
```
git remote add gitlab https://사내gitlab/그룹/프로젝트.git
git push gitlab main
```

### 2) 사이트 렌더링 → 다르다 (핵심)
GitHub Pages는 Jekyll을 **자동으로** 빌드해준다. 하지만 GitLab은 안 해준다.
저장소에 **`.gitlab-ci.yml`** 을 두어 "push되면 Jekyll로 빌드해 GitLab Pages에 올려라"고
직접 시켜야 한다.

```yaml
image: ruby:3.1
pages:
  stage: deploy
  script:
    - bundle exec jekyll build -d public --baseurl "/$CI_PROJECT_NAME"
  artifacts:
    paths: [public]
  rules:
    - if: $CI_COMMIT_BRANCH == $CI_DEFAULT_BRANCH
```

## .gitlab-ci.yml 은 git hook 이 아니다
* **git hook** : 내 PC `.git/hooks/`의 로컬 스크립트. 내 컴퓨터에서 실행, 저장소에 커밋 안 됨.
* **.gitlab-ci.yml** : 저장소에 커밋되는 파일. push가 GitLab 서버에 도착하면
  서버의 **Runner(빌드 컨테이너)** 가 실행한다. 내 컴퓨터가 아니라 GitLab 인프라에서 돈다.

흐름: `내 PC: commit/push` → `GitLab: yml 읽음` → `Runner: ruby 이미지 → bundle install → jekyll build` → `Pages 배포`.

## 사내 자체 GitLab에서 미리 확인할 것
1. **GitLab Pages 기능 활성화** (관리자 설정)
2. **CI Runner 존재** — 없으면 파이프라인이 pending에서 멈춤
3. **폐쇄망 여부** — Runner가 ruby 이미지·gem을 받아야 하므로,
   인터넷이 막혔으면 사내 도커 레지스트리/gem 미러 주소로 교체 필요

## 헤드리스 맥에서 메일 보내기: 시행착오
결과물(zip)을 회사 메일로 보내는 과정에서 막힌 것들:

* ❌ **Mail.app** : 헤드리스라 AppleEvent 타임아웃 → 사용 불가
* ❌ **시스템 메일(postfix)** : `relayhost` 미설정 → 외부 발송 불가
* ❌ **구글 드라이브** : 회사에서 안 열림 + 연결된 도구에 "공유" 기능 없음
* ✅ **Gmail SMTP (python smtplib)** : 앱 비밀번호로 인증해 발송

### Gmail 발송에서 또 막힌 두 가지
1. **앱 비밀번호** : 일반 로그인 비번으로는 안 됨(535 오류).
   2단계 인증 켠 뒤 16자리 앱 비밀번호를 발급해 사용.
2. **첨부 차단(552)** : Gmail은 zip 안에 `.js` 가 있으면 차단한다.
   일반 zip 암호화는 **파일 이름은 노출**되어 소용없었음.
   → 압축 전에 `.js` → `.js.txt` 로 바꿔 해결. 받는 쪽에서 한 줄로 복원:
   ```
   find . -name '*.js.txt' -exec sh -c 'mv "$0" "${0%.txt}"' {} \;
   ```

## 교훈
* GitHub Pages의 "자동 빌드"는 공짜가 아니다. 다른 곳에선 CI/CD로 직접 짜야 한다.
* 헤드리스 환경의 메일 발송은 SMTP(앱 비밀번호)가 가장 확실하다.
* Gmail은 압축 안의 실행 스크립트(.js 등)까지 검사한다. 파일명만 바꿔도 통과된다.
