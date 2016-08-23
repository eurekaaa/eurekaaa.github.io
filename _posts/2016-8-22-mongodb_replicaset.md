---
layout: post
comments: true
title:  "MongoDB Replica Set"
date:   2016-8-22
---

이번 포스트는 현재 사용중인 MongoDB에 Replication을 적용하기 위해 찾은 자료 중 [Replication/Replica Sets Part1.](http://cinema4dr12.tistory.com/entry/MongoDB-Replication-Replica-Sets)을 참고해서 진행했던 사항을 정리해 놓은 것이다.

1.
4대의 mongod를 사용하기로 하고 mongod1 ~ mongod4 총 4개의 디렉토리를 생성한다.

```shell
$ mkdir mongod1
$ mkdir mongod2
$ mkdir mongod3
$ mkdir mongod4
```

2.
mongod1 ~ 4 각 디렉토리 안에 conf, data, logs 디렉토리를 생성한다.

3.
2번에서 만든 각 conf 디렉토리마다 mongodb.conf 파일을 만들어 아래 내용을 입력한다.<br>
dbpath와 logpath는 각각 mongod[1-4] 로 설정하고 port는 [27017-27020]으로 설정한다.

```shell
dbpath=/home1/irteam/apps/mongodb/mongod1/data
logpath=/home1/irteam/apps/mongodb/mongod1/logs/mongodb.log
logappend=true
verbose=true

#bind_ip=127.0.0.1
port=27017
fork=true

rest=true
#auth=true
#noauth=true
```

4.
각 디렉토리 별로 mongod를 실행한다.
정상적으로 실행되면 아래와 같은 메시지가 출력된다.

```shell
$ mongod --dbpath ./mongod1 --replSet rs0 --smallfiles --oplogSize 128 --config mongod1/conf/mongodb.conf
[mongodb]$ mongod --dbpath ./mongod4 --replSet rs0 --smallfiles --oplogSize 128 --config mongod4/conf/mongodb.conf
2016-08-22T17:55:35.904+0900 I CONTROL  ** WARNING: --rest is specified without --httpinterface,
2016-08-22T17:55:35.904+0900 I CONTROL  **          enabling http interface
about to fork child process, waiting until server is ready for connections.
forked process: 19502
child process started successfully, parent exiting
```

아래와 같이 실행할 수도 있는데 forground process로 동작을 하기 때문에 위와 같이 실행한다.

```shell
$ mongod --port 27017 --dbpath ./mongod1 --replSet rs0 --smallfiles --oplogSize 128
2016-08-22T15:35:14.634+0900 I JOURNAL  [initandlisten] journal dir=./mongod1/journal
2016-08-22T15:35:14.634+0900 I JOURNAL  [initandlisten] recover : no journal files present, no recovery needed
2016-08-22T15:35:14.827+0900 I JOURNAL  [initandlisten] preallocateIsFaster=true 2.46
2016-08-22T15:35:15.023+0900 I JOURNAL  [initandlisten] preallocateIsFaster=true 2.48
2016-08-22T15:35:16.588+0900 I JOURNAL  [initandlisten] preallocateIsFaster=true 9.42
2016-08-22T15:35:16.588+0900 I JOURNAL  [initandlisten] preallocating a journal file ./mongod1/journal/prealloc.0
2016-08-22T15:35:18.852+0900 I JOURNAL  [initandlisten] preallocating a journal file ./mongod1/journal/prealloc.1
2016-08-22T15:35:21.029+0900 I JOURNAL  [initandlisten] preallocating a journal file ./mongod1/journal/prealloc.2
2016-08-22T15:35:23.313+0900 I JOURNAL  [durability] Durability thread started
2016-08-22T15:35:23.313+0900 I JOURNAL  [journal writer] Journal writer thread started
2016-08-22T15:35:23.317+0900 I CONTROL  [initandlisten] MongoDB starting : pid=13011 port=27017 dbpath=./mongod1 64-bit host=localhost
2016-08-22T15:35:23.317+0900 I CONTROL  [initandlisten] db version v3.0.7
2016-08-22T15:35:23.317+0900 I CONTROL  [initandlisten] git version: 6ce7cbe8c6b899552dadd907604559806aa2e9bd
2016-08-22T15:35:23.317+0900 I CONTROL  [initandlisten] build info: Linux build16.ny.cbi.10gen.cc 2.6.32-431.3.1.el6.x86_64 #1 SMP Fri Jan 3 21:39:27 UTC 2014 x86_64 BOOST_LIB_VERSION=1_49
2016-08-22T15:35:23.317+0900 I CONTROL  [initandlisten] allocator: tcmalloc
2016-08-22T15:35:23.317+0900 I CONTROL  [initandlisten] options: { net: { port: 27017 }, replication: { oplogSizeMB: 128, replSet: "rs0" }, storage: { dbPath: "./mongod1", mmapv1: { smallFiles: true } } }
2016-08-22T15:35:23.317+0900 I INDEX    [initandlisten] allocating new ns file ./mongod1/local.ns, filling with zeroes...
2016-08-22T15:35:24.651+0900 I STORAGE  [FileAllocator] allocating new datafile ./mongod1/local.0, filling with zeroes...
2016-08-22T15:35:24.651+0900 I STORAGE  [FileAllocator] creating directory ./mongod1/_tmp
2016-08-22T15:35:24.710+0900 I STORAGE  [FileAllocator] done allocating datafile ./mongod1/local.0, size: 16MB,  took 0.032 secs
2016-08-22T15:35:24.722+0900 I REPL     [initandlisten] Did not find local replica set configuration document at startup;  NoMatchingDocument Did not find replica set configuration document in local.system.replset
2016-08-22T15:35:24.722+0900 I NETWORK  [initandlisten] waiting for connections on port 27017
2016-08-22T15:40:37.213+0900 I NETWORK  [initandlisten] connection accepted from 127.0.0.1:49774 #1 (1 connection now open)
```

5.
mongod1에 실행한 mongodb에 접속한다.

```shell
$ mongo --port 27017
```

6.
PRIMARY 설정

```shell
> rsconf = {_id: "rs0", members:[{_id: 0, host: "localhost:27017"}]}
{
        "_id" : "rs0",
        "members" : [
                {
                        "_id" : 0,
                        "host" : "localhost:27017"
                }
        ]
}
```

7.
Replica Set 을 초기화 한다.

```shell
> rs.initiate(rsconf)
{ "ok" : 1 }
```
정상적으로 초기화 되면 ok가 값이 1이 된다.<br>
설정이 되고 나면 아래와 같이 프롬프트가 `rs0:OTHER>`로 변한다.

```shell
rs0:OTHER>
```

8.
Secondary db를 추가한다.

```shell
rs0:PRIMARY> rs.add("localhost:27018")
{ "ok" : 1 }
rs0:PRIMARY> rs.add("localhost:27019")
{ "ok" : 1 }
rs0:PRIMARY> rs.add("localhost:27020")
{ "ok" : 1 }
rs0:PRIMARY>
```

첫번째 Secondary를 추가하면 프롬프트가 아래와 같이 변경된다.

```shell
rs0:PRIMARY>
```

9.
Secondary에 접속하면 아래와 같은 프롬프트를 볼 수 있다.

```shell
rs0:SECONDARY>
```

10.
Primary에서 데이터를 생성해서 Secondary에 동기화 되는지 확인해 보자.

`replTestDB` 생성

```shell
use replTestDB
```

book collection에 데이터 삽입

```shell
rs0:PRIMARY> db.book.insert({title: 'Javascript X-ray', price: 100});
```

Secondary 중 하나에 접속

```shell
$ mongo --port 27020
```

Primary에서 생성한 데이터를 조회한다.

```shell
rs0:SECONDARY> db.book.find();
Error: error: { "$err" : "not master and slaveOk=false", "code" : 13435 }
```

하지만 에러가 발생한다. 이유는 메시지에 잘 명시되어 있다. master가 아니거나 slaveOk가 false 이어서 그렇단다.
`slaveOk=true`로 설정해서 다시 조회해 보자.

```shell
rs0:SECONDARY> rs.slaveOk();
rs0:SECONDARY> db.book.find();
{ "_id" : ObjectId("57bac6772f3bcf2ee6dc72fe"), "title" : "Jvascript X-ray", "price" : 100 }
```

11.
셋팅된 Replica Set을 확인해 보자.<br>
local db의 system.replset collection을 통해 확인이 가능하다.<br>
우선 local db로 전환하고 해당 collection에 find query를 실행하면 된다.

```shell
rs0:PRIMARY> use local
switched to db local
rs0:PRIMARY> db.system.replset.find().pretty();
{
        "_id" : "rs0",
        "version" : 4,
        "members" : [
                {
                        "_id" : 0,
                        "host" : "localhost:27017",
                        "arbiterOnly" : false,
                        "buildIndexes" : true,
                        "hidden" : false,
                        "priority" : 1,
                        "tags" : {

                        },
                        "slaveDelay" : 0,
                        "votes" : 1
                },
                {
                        "_id" : 1,
                        "host" : "localhost:27018",
                        "arbiterOnly" : false,
                        "buildIndexes" : true,
                        "hidden" : false,
                        "priority" : 1,
                        "tags" : {

                        },
                        "slaveDelay" : 0,
                        "votes" : 1
                },
                {
                        "_id" : 2,
                        "host" : "localhost:27019",
                        "arbiterOnly" : false,
                        "buildIndexes" : true,
                        "hidden" : false,
                        "priority" : 1,
                        "tags" : {

                        },
                        "slaveDelay" : 0,
                        "votes" : 1
                },
                {
                        "_id" : 3,
                        "host" : "localhost:27020",
                        "arbiterOnly" : false,
                        "buildIndexes" : true,
                        "hidden" : false,
                        "priority" : 1,
                        "tags" : {

                        },
                        "slaveDelay" : 0,
                        "votes" : 1
                }
        ],
        "settings" : {
                "chainingAllowed" : true,
                "heartbeatTimeoutSecs" : 10,
                "getLastErrorModes" : {

                },
                "getLastErrorDefaults" : {
                        "w" : 1,
                        "wtimeout" : 0
                }
        }
}
```

12.
Replica Set의 상태를 확인해 보자.

```shell
rs0:PRIMARY> rs.status();
{
        "set" : "rs0",
        "date" : ISODate("2016-08-23T02:31:44.103Z"),
        "myState" : 1,
        "members" : [
                {
                        "_id" : 0,
                        "name" : "localhost:27017",
                        "health" : 1,
                        "state" : 1,
                        "stateStr" : "PRIMARY",
                        "uptime" : 64163,
                        "optime" : Timestamp(1471858295, 2),
                        "optimeDate" : ISODate("2016-08-22T09:31:35Z"),
                        "electionTime" : Timestamp(1471855341, 1),
                        "electionDate" : ISODate("2016-08-22T08:42:21Z"),
                        "configVersion" : 4,
                        "self" : true
                },
                {
                        "_id" : 1,
                        "name" : "localhost:27018",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 62933,
                        "optime" : Timestamp(1471858295, 2),
                        "optimeDate" : ISODate("2016-08-22T09:31:35Z"),
                        "lastHeartbeat" : ISODate("2016-08-23T02:31:43.148Z"),
                        "lastHeartbeatRecv" : ISODate("2016-08-23T02:31:43.290Z"),
                        "pingMs" : 0,
                        "syncingTo" : "localhost:27017",
                        "configVersion" : 4
                },
                {
                        "_id" : 2,
                        "name" : "localhost:27019",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 61621,
                        "optime" : Timestamp(1471858295, 2),
                        "optimeDate" : ISODate("2016-08-22T09:31:35Z"),
                        "lastHeartbeat" : ISODate("2016-08-23T02:31:43.302Z"),
                        "lastHeartbeatRecv" : ISODate("2016-08-23T02:31:43.296Z"),
                        "pingMs" : 0,
                        "syncingTo" : "localhost:27020",
                        "configVersion" : 4
                },
                {
                        "_id" : 3,
                        "name" : "localhost:27020",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 62903,
                        "optime" : Timestamp(1471858295, 2),
                        "optimeDate" : ISODate("2016-08-22T09:31:35Z"),
                        "lastHeartbeat" : ISODate("2016-08-23T02:31:43.148Z"),
                        "lastHeartbeatRecv" : ISODate("2016-08-23T02:31:43.291Z"),
                        "pingMs" : 0,
                        "syncingTo" : "localhost:27017",
                        "configVersion" : 4
                }
        ],
        "ok" : 1
}
```

13.
만약 Primary 에 문제가 발생한다면 failover 시 새로운 Primary가 선택되어야 한다.<br>
이 결정은 Arbiter가 해준다. 자세한 Arbiter 설정은 아래 문서를 참고하자.<br>
"[Add an Arbiter to Replica Set](https://docs.mongodb.com/manual/tutorial/add-replica-set-arbiter/)"<br>
그리고 Arbiter를 구성할 때 주의할 점이 있는데, Arbiter를 Replica Set의 Member가 셋팅된 시스템에 설정하면 안된다.

> Do not run an arbiter on the same system as a member of the replica set.

Arbiter를 위한 디렉토리 생성

```shell
$ mkdir arb
$ mkdir arb/data
$ mkdir arb/conf
$ mkdir arb/logs
```

arb/conf 안레 Arbiter Config 파일 생성(mongodb.conf)

```shell
dbpath=/home1/irteam/apps/mongodb/arb/data
logpath=/home1/irteam/apps/mongodb/arb/logs/mongodb.log
logappend=true
verbose=true

#bind_ip=127.0.0.1
port=3000
fork=true

rest=true
#auth=true
#noauth=true
```

Arbiter를 실행한다.

```shell
[mongodb]$ mongod --port 30000 --dbpath ./arb/data/ --replSet rs --config ./arb/conf/mongodb.conf
2016-08-23T12:05:02.923+0900 I CONTROL  ** WARNING: --rest is specified without --httpinterface,
2016-08-23T12:05:02.924+0900 I CONTROL  **          enabling http interface
about to fork child process, waiting until server is ready for connections.
forked process: 122709
child process started successfully, parent exiting
```

Primary에 접속하고 Replica Set에 Arbiter를 추가한다.

```shell
rs0:PRIMARY> rs.addArb("localhost:30000")
{ "ok" : 1 }
```

14.
그럼 Primary에 문제가 발생했을 경우 Arbiter에 의해 새로운 Primary가 선택되는지 테스트 해보자.<br>
우선 현재 Primary를 shutdown한다. shutdown은 admin만 가능하다.

```shell
rs0:PRIMARY> use admin
switched to db admin
rs0:PRIMARY> db.shutdownServer();
2016-08-23T12:23:21.341+0900 I NETWORK  DBClientCursor::init call() failed
server should be down...
2016-08-23T12:23:21.343+0900 I NETWORK  trying reconnect to 127.0.0.1:27017 (127.0.0.1) failed
2016-08-23T12:23:21.343+0900 I NETWORK  reconnect 127.0.0.1:27017 (127.0.0.1) ok
2016-08-23T12:23:21.343+0900 I NETWORK  DBClientCursor::init call() failed
2016-08-23T12:23:21.345+0900 I NETWORK  trying reconnect to 127.0.0.1:27017 (127.0.0.1) failed
2016-08-23T12:23:21.346+0900 I NETWORK  reconnect 127.0.0.1:27017 (127.0.0.1) ok
2016-08-23T12:23:22.311+0900 I NETWORK  Socket recv() errno:104 Connection reset by peer 127.0.0.1:27017
2016-08-23T12:23:22.311+0900 I NETWORK  SocketException: remote: 127.0.0.1:27017 error: 9001 socket exception [RECV_ERROR] server [127.0.0.1:27017]
2016-08-23T12:23:22.311+0900 I NETWORK  DBClientCursor::init call() failed
>
```

shutdown을 하고나면 프로프트가 `rs0:PRIMARY>`에서 `>`로 변경된다.

그럼 Secondary 중에 아무곳 하나에 접근해서 현재 Replica Set의 상태를 확인하고 어떤게 Primary로 선택되었는지 확인해 보자.

```shell
rs0:SECONDARY> rs.status();
{
        "set" : "rs0",
        "date" : ISODate("2016-08-23T03:23:53.396Z"),
        "myState" : 1,
        "members" : [
                {
                        "_id" : 0,
                        "name" : "localhost:27017",
                        "health" : 0,
                        "state" : 8,
                        "stateStr" : "(not reachable/healthy)",
                        "uptime" : 0,
                        "optime" : Timestamp(0, 0),
                        "optimeDate" : ISODate("1970-01-01T00:00:00Z"),
                        "lastHeartbeat" : ISODate("2016-08-23T03:23:52.444Z"),
                        "lastHeartbeatRecv" : ISODate("2016-08-23T03:23:20.421Z"),
                        "pingMs" : 0,
                        "lastHeartbeatMessage" : "Failed attempt to connect to localhost:27017; couldn't connect to server localhost:27017 (127.0.0.1), connection attempt failed",
                        "configVersion" : -1
                },
                {
                        "_id" : 1,
                        "name" : "localhost:27018",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 66033,
                        "optime" : Timestamp(1471922242, 1),
                        "optimeDate" : ISODate("2016-08-23T03:17:22Z"),
                        "lastHeartbeat" : ISODate("2016-08-23T03:23:52.430Z"),
                        "lastHeartbeatRecv" : ISODate("2016-08-23T03:23:52.436Z"),
                        "pingMs" : 0,
                        "lastHeartbeatMessage" : "could not find member to sync from",
                        "configVersion" : 5
                },
                {
                        "_id" : 2,
                        "name" : "localhost:27019",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 64750,
                        "optime" : Timestamp(1471922242, 1),
                        "optimeDate" : ISODate("2016-08-23T03:17:22Z"),
                        "lastHeartbeat" : ISODate("2016-08-23T03:23:52.434Z"),
                        "lastHeartbeatRecv" : ISODate("2016-08-23T03:23:52.433Z"),
                        "pingMs" : 0,
                        "configVersion" : 5
                },
                {
                        "_id" : 3,
                        "name" : "localhost:27020",
                        "health" : 1,
                        "state" : 1,
                        "stateStr" : "PRIMARY",
                        "uptime" : 66498,
                        "optime" : Timestamp(1471922242, 1),
                        "optimeDate" : ISODate("2016-08-23T03:17:22Z"),
                        "electionTime" : Timestamp(1471922602, 1),
                        "electionDate" : ISODate("2016-08-23T03:23:22Z"),
                        "configVersion" : 5,
                        "self" : true
                },
                {
                        "_id" : 4,
                        "name" : "localhost:30000",
                        "health" : 0,
                        "state" : 8,
                        "stateStr" : "(not reachable/healthy)",
                        "uptime" : 0,
                        "lastHeartbeat" : ISODate("2016-08-23T03:23:52.599Z"),
                        "lastHeartbeatRecv" : ISODate("1970-01-01T00:00:00Z"),
                        "configVersion" : -1
                }
        ],
        "ok" : 1
}
rs0:PRIMARY>
```

_id가 3인 노드가 Primary로 선택된 것을 확인할 수 있다.<br>

그럼 _id가 0인 원래 Primary였던 노드를 다시 살려보자.<br>

```shell
[mongodb]$ mongod --dbpath ./mongod1 --replSet rs0 --smallfiles --oplogSize 128 --config mongod1/conf/mongodb.conf
2016-08-23T12:32:34.307+0900 I CONTROL  ** WARNING: --rest is specified without --httpinterface,
2016-08-23T12:32:34.307+0900 I CONTROL  **          enabling http interface
about to fork child process, waiting until server is ready for connections.
forked process: 127973
child process started successfully, parent exiting
```

mongo에 접속해보자

```shell
[irteam@dev-daltong.ncl mongodb]$ mongo --port 27017
MongoDB shell version: 3.0.7
connecting to: 127.0.0.1:27017/test
Server has startup warnings:
2016-08-23T12:32:34.307+0900 I CONTROL  ** WARNING: --rest is specified without --httpinterface,
2016-08-23T12:32:34.307+0900 I CONTROL  **          enabling http interface
rs0:SECONDARY>
```

프롬프트가 `rs0:SECONDARY>`로 되어 있다. 기존에 Primary였다해도 다시 Primary가 되지는 않는다.<br>
Replica Set의 Status를 확인해 보면 좀전에 선택된 `_id: 3` 인 노드가 여전히 Primary인 상태인 것을 확인 할 수 있다.

```shell
rs0:SECONDARY> rs.status();
{
        "set" : "rs0",
        "date" : ISODate("2016-08-23T03:35:05.563Z"),
        "myState" : 2,
        "members" : [
                {
                        "_id" : 0,
                        "name" : "localhost:27017",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 151,
                        "optime" : Timestamp(1471922242, 1),
                        "optimeDate" : ISODate("2016-08-23T03:17:22Z"),
                        "configVersion" : 5,
                        "self" : true
                },
                {
                        "_id" : 1,
                        "name" : "localhost:27018",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 151,
                        "optime" : Timestamp(1471922242, 1),
                        "optimeDate" : ISODate("2016-08-23T03:17:22Z"),
                        "lastHeartbeat" : ISODate("2016-08-23T03:35:04.455Z"),
                        "lastHeartbeatRecv" : ISODate("2016-08-23T03:35:05.499Z"),
                        "pingMs" : 0,
                        "lastHeartbeatMessage" : "could not find member to sync from",
                        "configVersion" : 5
                },
                {
                        "_id" : 2,
                        "name" : "localhost:27019",
                        "health" : 1,
                        "state" : 2,
                        "stateStr" : "SECONDARY",
                        "uptime" : 151,
                        "optime" : Timestamp(1471922242, 1),
                        "optimeDate" : ISODate("2016-08-23T03:17:22Z"),
                        "lastHeartbeat" : ISODate("2016-08-23T03:35:04.455Z"),
                        "lastHeartbeatRecv" : ISODate("2016-08-23T03:35:05.498Z"),
                        "pingMs" : 0,
                        "configVersion" : 5
                },
                {
                        "_id" : 3,
                        "name" : "localhost:27020",
                        "health" : 1,
                        "state" : 1,
                        "stateStr" : "PRIMARY",
                        "uptime" : 151,
                        "optime" : Timestamp(1471922242, 1),
                        "optimeDate" : ISODate("2016-08-23T03:17:22Z"),
                        "lastHeartbeat" : ISODate("2016-08-23T03:35:04.455Z"),
                        "lastHeartbeatRecv" : ISODate("2016-08-23T03:35:05.503Z"),
                        "pingMs" : 0,
                        "electionTime" : Timestamp(1471922602, 1),
                        "electionDate" : ISODate("2016-08-23T03:23:22Z"),
                        "configVersion" : 5
                },
                {
                        "_id" : 4,
                        "name" : "localhost:30000",
                        "health" : 0,
                        "state" : 8,
                        "stateStr" : "(not reachable/healthy)",
                        "uptime" : 0,
                        "lastHeartbeat" : ISODate("2016-08-23T03:35:04.513Z"),
                        "lastHeartbeatRecv" : ISODate("1970-01-01T00:00:00Z"),
                        "configVersion" : -1
                }
        ],
        "ok" : 1
}
rs0:SECONDARY>
```




## Appendix

* [기본적인 MongoDB CRUD](https://velopert.com/457)

