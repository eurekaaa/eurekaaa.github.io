---
layout: post
comments: true
title:  "Git Page에 Jekyll Bootstrap 적용 시 참고사항"
date:   2016-6-8
---


git page에 jekyll bootstrap을 적용하는 방법은 아래 링크를 참고하면 된다.
<ul>
<li>
  <a href="http://jekyllbootstrap.com/usage/jekyll-quick-start.html">Jekyll QuickStart</a>
</li>
</ul>

이번 포스트에서는 jekyll bootstrap 적용 시 몇 가지 문제가 있었는데, 그에대한 해결 방법을 작성한 내용이다.


### 서브모듈 추가 하기
jekyll bootstrap의 theme 적용 시 .gitsubmodules에 파일에 서브 모듈의 정보를 추가해야 github에서 정상적으로 build된다.<br>
.gitsubmodules 파일이 없으면 theme 설치 후 push를 하게되면 github에서 build error가 발생하고 github에 등록된 e-mail로 error mail이 온다.<br>
서브모듈은 .gitmodules 파일을 통해 관리된다.<br>
git 프로젝트의 root directory에 .gitmodules파일을 추가하고 추가를 원하는 모듈의 root directory path와 원격 저장소 주소를 넣어주면된다.

```bash
submodule "_theme_packages/twitter"
  path = _theme_packages/twitter
  url = https://github.com/jekyllbootstrap/theme-twitter.git
```

### github에 SSH key 등록하기
git에 SSH key를 등록하여 사용할 수 있다.

#### 1. ssh 공용키 생성

```bash
$ cd ~/.ssh
$ ssh-keygen -t rsa -C "your-email@example.com"
```

#### 2. 생성된 id_rsa.pub의 파일 내용 복사

#### 3. github의 settings > SSH and GPG keys 로 이동 후 "NEW SSH Key" 클릭하여 복사한 내용 저장



