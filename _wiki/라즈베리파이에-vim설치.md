---
layout  : wiki
title   : 
summary : 
date    : 2020-06-24 13:04:42 +0900
updated : 2020-06-26 01:43:41 +0900
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
---
* TOC
{:toc}


* vim+python 설치 관련글 https://qastack.kr/vi/11526/how-to-enable-python-feature-in-vim
* 여기서 '$which python', '$which python3'를 사용하면 python 설치 경로를 알 수 있다.
* 위 설치 글에서 python 디렉토리 입력에 위 경로를 주의해서 입력하자.
* "checkinstall"이 데미안 명령어로 설치가 가능함. "$sudo apt-get install checkinstall"
* 설치 후에 만약 삭제원하면 "$dpkg -r vim"으로 삭제 할 수 있다고.
![image](https://user-images.githubusercontent.com/43912095/85499653-fa209400-b61c-11ea-86d6-70081503b732.png)
* 장시간의 사투 끝에 python이 vim에서 동작하지 않아서 vim-nox를 설치하니 일단 동작은 한다.
* 그러나 vim8.0이어서 vimgo 실행이 제대로 동작안한다고 메시지가 나오고 실행중간중간 오류메시지를 확인할 수 있다.
* 위의 설치 기록은 라즈베리파이3에 osmc 를 설치했다.
* 추가로 우분투를 설치해보니 기본으로 python3에 vim8.1까지 완벽하게 깔려있다.
* 우분투가 답이다.
