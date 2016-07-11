---
layout: post
comments: true
title:  "How to set ffmpeg on centos"
date:   2016-6-3
---

<ol>
<li>
ffmpeg을 컴파일 하기 위한 dependency가 있는 라이브러리들을 설치한다. 당연히 관리자 권한으로 설치를 진행해야 한다.
{% highlight bash %}
$ sudo yum install autoconf automake cmake freetype-devel gcc gcc-c++ git libtool make mercurial nasm pkgconfig zlib-devel
{% endhighlight %}
아래와 같이 라이브러리들을 다운로드 하겠냐고 물어보면 'y'를 선택한다.
<img src='{{site.url}}/assets/imgs/setting_ffmpeg0.jpg'>
정상적으로 설치가 되면 아래와 같이 출력된다.
<img src='{{site.url}}/assets/imgs/setting_ffmpeg1.jpg'>
</li>
<li>
ffmpeg_sources 폴더 생성(이후부터는 su로 설치하지 않아도된다.)<br>
ffmpeg과 관련된 소스를 다운로드 받을 폴더를 생성한다.
{% highlight bash %}
$ mkdir ~/ffmpeg_sources
{% endhighlight %}
</li>

<li>
Yasm 설치
{% highlight bash %}
$ cd ~/ffmpeg_sources
$ git clone https://github.com/yasm/yasm.git
$ cd yasm
$ autoreconf -fiv
$ ./configure --prefix="$HOME/ffmpeg_build" --bindir="$HOME/bin"
$ make
$ make install
$ make distclean
{% endhighlight %}
</li>
<li>
libx264 설치<br>
H.264 비디오 인코더를 설치한다. 
{% highlight bash %}
$ cd ~/ffmpeg_sources
$ git clone http://git.videolan.org/git/x264.git
$ cd x264
$ ./configure --prefix="$HOME/ffmpeg_build" --bindir="$HOME/bin" --enable-static
$ make
$ make install
$ make distclean
{% endhighlight %}
</li>
<li>
H.265/HEVC 비디오 인코더를 설치한다. 
{% highlight bash %}
$ cd ~/ffmpeg_sources
$ hg clone https://bitbucket.org/multicoreware/x265
$ cd ~/ffmpeg_sources/x265/build/linux
$ cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX="$HOME/ffmpeg_build" -DENABLE_SHARED:bool=off ../../source
$ make
$ make install
{% endhighlight %}
</li>
<li>
ibfdk-aac 설치<br>
AAC 오디오 인코더를 설치한다.
{% highlight bash %}
$ cd ~/ffmpeg_sources
$ git clone https://github.com/mstorsjo/fdk-aac.git
$ cd fdk-aac
$ autoreconf -fiv
$ ./configure --prefix="$HOME/ffmpeg_build" --disable-shared
$ make
$ make install
$ make distclean
{% endhighlight %}
</li>
<li>
libmp3lame 설치<br>
MP3 오디오 인코더를 설치한다. 
{% highlight bash %}
$ cd ~/ffmpeg_sources
$ curl -L -O http://downloads.sourceforge.net/project/lame/lame/3.99/lame-3.99.5.tar.gz
$ tar xzvf lame-3.99.5.tar.gz
$ cd lame-3.99.5
$ ./configure --prefix="$HOME/ffmpeg_build" --bindir="$HOME/bin" --disable-shared --enable-nasm
$ make
$ make install
{% endhighlight %}
</li>
<li>
libogg 설치<br>
Ogg bitstream 라이브러리를 설치한다.
{% highlight bash %}
$ cd ~/ffmpeg_sources
$ curl -O http://downloads.xiph.org/releases/ogg/libogg-1.3.2.tar.gz
$ tar xzvf libogg-1.3.2.tar.gz
$ cd libogg-1.3.2
$ ./configure --prefix="$HOME/ffmpeg_build" --disable-shared
$ make
$ make install
$ make distclean
{% endhighlight %}
</li>
<li>
libvorbis 설치<br>
Vorbis 오디오 인코더를 설치한다. 설치시 libogg가 필요하다.
{% highlight bash %}
$ cd ~/ffmpeg_sources
$ curl -O http://downloads.xiph.org/releases/vorbis/libvorbis-1.3.4.tar.gz
$ tar xzvf libvorbis-1.3.4.tar.gz
$ cd libvorbis-1.3.4
$ LDFLAGS="-L$HOME/ffmeg_build/lib" CPPFLAGS="-I$HOME/ffmpeg_build/include" ./configure --prefix="$HOME/ffmpeg_build" --with-ogg="$HOME/ffmpeg_build" --disable-shared
$ make
$ make install
$ make distclean
{% endhighlight %}
</li>
<li>
libvpx 설치<br>
VP8/VP9 비디오 인코더를 설치한다.
{% highlight bash %}
$ cd ~/ffmpeg_sources
$ git clone --depth 1 https://chromium.googlesource.com/webm/libvpx.git
$ cd libvpx
$ ./configure --prefix="$HOME/ffmpeg_build" --disable-examples
$ make
$ make install
$ make clean
{% endhighlight %}
</li>
<li>
ffmpeg 설치
{% highlight bash %}
$ cd ~/ffmpeg_sources
$ git clone https://github.com/FFmpeg/FFmpeg.git
$ cd FFmpeg
$ PKG_CONFIG_PATH="$HOME/ffmpeg_build/lib/pkgconfig" ./configure --prefix="$HOME/ffmpeg_build" --extra-cflags="-I$HOME/ffmpeg_build/include" --extra-ldflags="-L$HOME/ffmpeg_build/lib" --bindir="$HOME/bin" --pkg-config-flags="--static" --enable-gpl --enable-nonfree --enable-libfdk-aac --enable-libfreetype --enable-libmp3lame --enable-libvorbis --enable-libvpx --enable-libx264 --enable-libx265
$ make
$ make install
$ make distclean
$ hash -r
{% endhighlight %}
</li>
</ol>

<h1>라이브러리 업데이트 시</h1>
<ul>
	<li>
	ffmpeg Update 시<br>
{% highlight bash %}
$ rm -rf ~/ffmpeg_build ~/bin/{ffmpeg, ffprobe, ffserver, lame, vsyasm, x264, x265, yasm, ytasm}
{% endhighlight %}
	</li>

	<li>
	update Yasm
{% highlight bash %}
$ cd ~/ffmpeg_sources/yasm
$ make distclean
$ git pull
{% endhighlight %}
	# pull이 완료되면 Yasm 설치 시 언급한 ./configure, make, make install 진행.
	</li>

	<li>
	update x264
{% highlight bash %}
$ cd ~/ffmpeg_sources/x264
$ make distclean
$ git pull
{% endhighlight %}
	# pull이 완료되면 x264 설치 시 언급한 ./configure, make, make install 진행.
	</li>

	<li>
	update x265
{% highlight bash %}
$ cd ~/ffmpeg_sources/x265
$ rm -rf ~/ffmpeg_sources/x265/build/linux/*
$ hg update
$ cd ~/ffmpeg_sources/x265/build/linux
{% endhighlight %}
	# 이 후, x265 설치 시 언급한 cmake, make, make install 진행.
	</li>

	<li>
	update libfdk_aac
{% highlight bash %}
$ cd ~/ffmpeg_sources/fdk_aac
$ make distclean
$ git pull
{% endhighlight %}
	# pull이 완료되면 libfdk_aac 설치 시 언급한 ./configure, make, make install 진행.
	</li>

	<li>
	update libvpx
{% highlight bash %}
$ cd ~/ffmpeg_sources/libvpx
$ make clean
$ git pull
{% endhighlight %}
	# pull이 완료되면 libvpx 설치 시 언급한 ./configure, make, make install을 진행.
	</li>

	<li>
	# update FFmpeg
{% highlight bash %}
$ cd ~/ffmpeg_sources/ffmpeg
$ make distclean
$ git pull
{% endhighlight %}
	# pull이 완료되면 ffmpeg 설치 시 언급한 ./configure, make, make install을 진행.
	</li>
	
	<li>
	ffmpeg 삭제 시
{% highlight bash %}
$ rm -rf ~/ffmpeg_build ~/ffmpeg_sources ~/bin/{ffmpeg, ffprobe, ffserver, lame, vsyasm, x264, yasm, ytasm}
$ hash -r
{% endhighlight %}
	</li>
</ul>
