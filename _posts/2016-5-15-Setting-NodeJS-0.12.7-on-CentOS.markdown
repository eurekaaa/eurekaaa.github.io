---
layout: post
title:  "Setting NodeJS(0.12.7) on CenterOS"
date:   2016-5-15
---

<ol>
<li>
특정 디렉토리를 생성 후(여기서는 apps 디렉토리를 생성했다.) NodeJS 0.12.7 버전을 다운 받는다.
<br>(현재 OracleDB 연결은 0.12.7에서 지원한다.)
{% highlight bash %}
$ wget https://nodejs.org/download/release/v0.12.7/node-v0.12.7-linux-x64.tar.gz
{% endhighlight %}
</li>
<li>
다운 받은 tar 파일 압축 해제
{% highlight bash %}
$ cd apps
$ tar -zxf node-v0.12.7-linux-x64.tar.gz
{% endhighlight %}
</li>
<li>
NodeJS HOME 설정

.bash_profile를 vi로 오픈 후
{% highlight bash %}
$ cd ~
$ vi .bash_profile
{% endhighlight %}
NODE_HOME을 PATH에 추가한다.
{% highlight bash %}
NODE_HOME=/home1/irteam/apps/node-v0.12.7-linux-x64
PATH=$PATH:$HOME/bin:$NODE_HOME/bin
{% endhighlight %}
추가한 NODE_HOME을 시스템변수에 등록한다.
{% highlight bash %}
$ source .bash_profile
$ echo $PATH
{% endhighlight %}
위에 추가한 NODE_HOME의 path가 아래와 같이 출력되면 정상적으로 등록된 것이다.
<img src='http://localhost:4000/assets/imgs/setting_nodejs_0.jpg'>
</li>
<li>
정상적으로 노드가 설치 되었는지 버전을 확인하자.
{% highlight bash %}
$ node -v
{% endhighlight %}
또는
{% highlight bash %}
$ node --version
{% endhighlight %}
<img src='http://localhost:4000/assets/imgs/setting_nodejs_1.jpg'>
</li>
