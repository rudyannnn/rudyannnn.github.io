---
layout  : wiki
title   : CI, CD, gem, 도커 이미지 개념 정리
summary : GitLab Pages로 블로그를 빌드하며 익힌 핵심 용어
date    : 2026-06-06 18:10:00 +0900
updated : 2026-06-06 18:10:00 +0900
tag     : ci cd gem docker gitlab jekyll
toc     : true
public  : true
parent  : [[index]]
latex   : false
---
* TOC
{:toc}

## 이 문서는?
이 블로그(GitHub Pages)를 회사 GitLab에도 똑같이 띄워보려다 만난 용어들
(CI, CD, gem, 도커 이미지)을 정리한 메모.

GitHub Pages는 Jekyll 빌드를 자동으로 해주지만, GitLab에서는
`.gitlab-ci.yml`이라는 파일로 직접 빌드를 시켜야 한다. 그 과정에서 나온 개념들이다.

## CI (Continuous Integration, 지속적 통합)
* 코드를 push할 때마다 **자동으로 빌드·검사**해주는 것.
* 옛날엔 사람이 직접 빌드/테스트 → 깜빡하거나 실수.
* 지금은 push하면 서버가 자동으로 "빌드 되나? 테스트 통과하나?" 확인.
* 이 블로그라면 → push할 때마다 **Jekyll로 사이트가 제대로 빌드되는지** 자동 확인하는 부분.

## CD (Continuous Delivery/Deployment, 지속적 배포)
* 빌드가 끝나면 **자동으로 실제 서비스에 올려주는 것**. CI 다음 단계.
* 이 블로그라면 → 빌드된 `public/` 폴더를 **GitLab Pages에 자동 배포**하는 부분.
* 보통 묶어서 **CI/CD 파이프라인**이라 부른다.

```
push → [CI: 빌드·검사] → [CD: 배포] → 사이트 반영
```

## gem (잼)
* **Ruby 언어의 라이브러리(부품) 패키지.** 파이썬 `pip`, node `npm` 패키지와 같은 개념.
* 이 블로그의 `Gemfile`에 적힌 게 다 gem이다.
    * `jekyll` : 블로그를 만들어주는 핵심 gem
    * `jekyll-paginate` : 글 목록 페이지 나누기
    * `webrick` : 로컬 서버 띄우기
* `bundle install` = "Gemfile에 적힌 gem들을 rubygems.org에서 받아 설치하라".
* 폐쇄망이면 gem을 못 받아와서 문제가 생긴다 (사내 gem 미러 필요).

## 도커 이미지 (Docker image)
* **프로그램 + 실행환경을 통째로 담아둔 '냉동 도시락'.**
* 빌드를 돌리려면 환경 안에 Ruby가 깔려 있어야 한다. 매번 설치하면 번거롭고
  환경이 제각각이 되므로, "Ruby가 미리 설치된 깨끗한 환경"을 박제해 둔 것.
* `.gitlab-ci.yml`의 `image: ruby:3.1` = "Ruby 3.1이 깔린 도커 이미지로 빌드하라".
* Runner는 이 이미지로 **일회용 컨테이너**를 띄워 빌드한 뒤 버린다 → 항상 같은 깨끗한 환경 보장.

## 하나로 꿰어보기
```
push
 → GitLab이 .gitlab-ci.yml 읽음
 → Runner가 [ruby:3.1 도커 이미지]로 깨끗한 환경을 띄움
 → 그 안에서 bundle install (gem 설치) → jekyll build   ← CI
 → 결과(public/)를 GitLab Pages에 배포                  ← CD
 → 사이트 반영
```

## 덧: git hook 과의 차이
* **git hook** : 내 컴퓨터 `.git/hooks/`에 있는 로컬 스크립트. 내 PC에서 실행되고 저장소에 커밋되지 않는다.
* **`.gitlab-ci.yml`** : 저장소에 커밋되는 파일. push가 GitLab 서버에 도착하면 **서버의 Runner**가 실행한다.
* 즉 CI/CD는 hook이 아니라 **서버 쪽 자동화**다.
