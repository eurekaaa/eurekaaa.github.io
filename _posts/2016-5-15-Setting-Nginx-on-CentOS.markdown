---
layout: post
comments: true
title:  "How to set nginx on centos and work with nodejs"
date:   2016-5-15 16:40:00 +0900
---

<ol>
<li>
nginx 설치는 yum을 사용한다. 권한을 위해 su로 접근해야한다.
<br> 아래 명령어로 설치를 시작한다. 중간에 "Is this ok [y/N]:" 이렇게 물어보면 y를 선택하면 된다. 설치 파일을 다운 받을 것인지 물어보는 것이다.
{% highlight bash %}
$ sudo yum install nginx
{% endhighlight %}
아래와 같은 내용이 출력되면 설치가 완료된 것이다.
<img src='{{site.url}}/assets/imgs/setting_nginx_0.jpg'>
</li>
<li>
nginx를 실행하자.
<br>설치 후 실행 권한이 없기 때문에 실행이 되지 않는다.
<br>yum으로 설치하면 init script는 /etc/init.d/nginx 에 이미 생성되어 있다.
<br>아래 명령어로 권한 및 시작프로그램을 등록하자
첫 번째는 실행권한을 부여
두 번째는 운영체제 구동 시 실행이 되도록 설정
세 번째는 정상적으로 등록됐는지 확인
{% highlight bash %}
$ sudo chmod +x /etc/init.d/nginx
$ sudo /sbin/chkconfig nginx on
$ sudo /sbin/chkconfig --list nginx
{% endhighlight %}
<img src='{{site.url}}/assets/imgs/setting_nginx_1.jpg'>
</li>
<li>
이제 nginx를 실행하자.
{% highlight bash %}
$ sudo /etc/init.d/nginx start
{% endhighlight %}
아래와 같이 OK가 출력되면 정상적으로 실행된 것이다.
<img src='{{site.url}}/assets/imgs/setting_nginx_2.jpg'>
<br><br>브라우저로 확인해 보자
<br>
<img src='{{site.url}}/assets/imgs/setting_nginx_3.jpg'>
</li>
<li>
nodejs와 nginx를 연동하자.
아래 명령어를 통해 nginx의 설정 파일을 아래와 같이 수정한다.
{% highlight bash %}
$ sudo vi /etc/nginx/conf.d/default.conf
{% endhighlight %}

AS-IS
{% highlight bash %}
location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
}
{% endhighlight %}

TO-BE
{% highlight bash %}
location / {
    root   /usr/share/nginx/html;
    index  index.html index.htm;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header HOST $http_host;
    proxy_set_header X-NginX-Proxy true;

    proxy_pass http://127.0.0.1:3000/;
    proxy_redirect off;
}
{% endhighlight %}

실제 수정된 파일, 아래 빨간 박스 영역이 추가되었다.<br>
<img src='{{site.url}}/assets/imgs/setting_nginx_4.jpg'>
</li>
<li>
nginx를 재실행 한다.
{% highlight bash %}
$ sudo /etc/init.d/nginx restart
{% endhighlight %}
<img src='{{site.url}}/assets/imgs/setting_nginx_5.jpg'>
</li>
