---
layout: post
comments: true
title:  "MongoDB 설치"
date:   2016-8-22
---

1.
mongoDB 다운로드

```shell
$ curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.0.7.tgz
```

2.
압축해제

```shell
$ tar -xvf mongodb-linux-x86_64-3.0.7.tgz
```

3.
심볼릭 링크 생성

```shell
$ ln -s mongodb-linux-x86_64-3.0.7 mongodb
```

4.
환경변수 설정

```shell
$ vi ~/.bash_profile
```

MONGO_HOME을 추가하고 PATH에 $MONGO_HOME/bin을 추가한다.

```shell
MONGO_HOME=/home1/irteam/apps/mongodb
PATH=$PATH:$HOME/bin:$MONGO_HOME/bin
```

설정된 환경변수를 시스템에 적용한다.

```shell
$ source ~/.bash_profile
```

5.
압축해제한 mongodb 디렉토리를 이동 후 아래 세개의 디렉토리 생성

```shell
$ mkdir data
$ mkdir conf
$ mkdir logs
```

6.
위에서 생성한 conf 디렉토리로 이동 후 mongodb.conf 파일 생성 후 아래 내용을 설정한다.

```shell
$ cd conf
$ vi mongodb.conf
```

dbpath와 logpath는 5번에서 생성한 디렉토리의 path로 설정하면 된다.

```shell
dbpath=/home1/irteam/apps/mongodb/data
logpath=/home1/irteam/apps/mongodb/logs/mongodb.log
logappend=true
verbose=true

#bind_ip=127.0.0.1
port=27017
fork=true

rest=true
#auth=true
#noauth=true
```

7.
mongodb 실행<br>
정상적으로 실행이되면 아래와 같이 출력된다.

```shell
$ mongod --config conf/mongodb.conf
2016-08-22T15:14:44.406+0900 I CONTROL  ** WARNING: --rest is specified without --httpinterface,
2016-08-22T15:14:44.406+0900 I CONTROL  **          enabling http interface
about to fork child process, waiting until server is ready for connections.
forked process: 12122
child process started successfully, parent exiting
```





