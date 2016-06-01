---
layout: post
title:  "phantomjs에서 한글 깨짐 현상 해결 방법"
date:   2016-5-31
---

리눅스에서 <a href="http://phantomjs.org/">PhantomJS</a>, <a href="https://github.com/amir20/phantomjs-node">Phantom(NodeJS Module)</a>, <a href="http://casperjs.org/">CasperJS</a> 또는 <a href="https://github.com/SpookyJS/SpookyJS">SpookyJS</a>를 사용하여 웹페이지를 capture할 때 한글이 깨지는 현상이 있다.
이는 리눅스 서버에 폰트가 설치되어 있지 않아서 발생하는 현상이다.

한글이 설치 되지 않은 경우 아래와 같이 폰트가 깨진다.
<img src='{{site.url}}/assets/imgs/capture1.png'>
<br><br>
<h1>해결 방법</h1>
'/usr/share/fonts' 디렉토리에 설치하고자 하는 폰트를 복사한 후 아래 명령어를 실행한다.<br>
윈도우의 바탕체, 굴림체, 나눔고딕체 정도만 설치해도 된다.

{% highlight bash %}
$ fc-cache -f -v
/usr/share/fonts: caching, new cache contents: 24 fonts, 1 dirs
/usr/share/fonts/default: caching, new cache contents: 0 fonts, 2 dirs
/usr/share/fonts/default/Type1: caching, new cache contents: 35 fonts, 0 dirs
/usr/share/fonts/default/ghostscript: caching, new cache contents: 13 fonts, 0 dirs
/usr/share/X11/fonts/Type1: skipping, no such directory
/usr/share/X11/fonts/TTF: skipping, no such directory
/usr/local/share/fonts: skipping, no such directory
/home1/irteamsu/.fonts: skipping, no such directory
/var/cache/fontconfig: not cleaning unwritable cache directory
/home1/irteamsu/.fontconfig: cleaning cache directory
fc-cache: succeeded
{% endhighlight %}

위와 같이 폰트를 추가하고 나면 정상적으로 캡쳐가된다.
<img src='{{site.url}}/assets/imgs/capture2.png'>
