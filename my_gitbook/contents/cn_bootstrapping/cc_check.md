---
# permalink: "cn_bootstrapping/cc_check"
layout: "single"
sidebar:
  nav: "cn_bootstrapping"
---
# 설치 확인

Klaytn은 다음의 방법으로 Klaytn 노드 (CN, PN)의 상태를 확인할 수 있다. 

- status 명령어 (klayd, systemctl)
- 로그 확인
- klay console에서 상태 확인

## status 명령어

status 명령어로 CN,PN 프로세스의 상태를 확인할 수 있다.

### systemctl (RPM으로 설치했을 경우)

```
$ systemctl status klay.service
● klay.service - (null)
   Loaded: loaded (/etc/rc.d/init.d/klay; bad; vendor preset: disabled)
   Active: active (running) since Wed 2019-01-09 11:42:39 UTC; 1 months 4 days ago
     Docs: man:systemd-sysv-generator(8)
  Process: 29636 ExecStart=/etc/rc.d/init.d/klay start (code=exited, status=0/SUCCESS)
 Main PID: 29641 (klay)
   CGroup: /system.slice/klay.service
           └─29641 /usr/local/bin/klay --nodetype cn --networkid 1000 --datadir /data/klay --port 32323 --srvtype fasthttp --metrics --prometheus --verbosity 3 --txpool.global...

Jan 09 11:42:39 ip-10-11-2-101.ap-northeast-2.compute.internal systemd[1]: Starting (null)...
Jan 09 11:42:39 ip-10-11-2-101.ap-northeast-2.compute.internal klay[29636]: Starting klay (CN): [  OK  ]
Jan 09 11:42:39 ip-10-11-2-101.ap-northeast-2.compute.internal systemd[1]: Started (null).
```

```Active: active (running)``` 와 같이 프로세스의 현재 상태를 확인할 수 있다.


### klayd (패키지로 설치했을 경우)

```
$ klayd
klay is running
```

## 로그 확인 

klay.conf의 LOG_DIR 속성에 지정된 디렉토리로 klay.out 로그파일을 확인 할 수 있다. 정상 동작시 블록이 1초 단위로 생성되는 것을 확인할 수 있다.

로그 예)
```
$ tail klay.out
INFO[02/13,07:02:24 Z] [35] Commit new mining work                    number=11572924 txs=0 uncles=0 elapsed=488.336µs
INFO[02/13,07:02:25 Z] [5] Imported new chain segment                blocks=1 txs=0 mgas=0.000     elapsed=1.800ms   mgasps=0.000       number=11572924 hash=f46d09…ffb2dc cache=1.59mB
INFO[02/13,07:02:25 Z] [35] Commit new mining work                    number=11572925 txs=0 uncles=0 elapsed=460.485µs
INFO[02/13,07:02:25 Z] [35] 🔗 block reached canonical chain           number=11572919 hash=01e889…524f02
INFO[02/13,07:02:26 Z] [14] Committed                                 address=0x1d4E05BB72677cB8fa576149c945b57d13F855e4 hash=1fabd3…af66fe number=11572925
INFO[02/13,07:02:26 Z] [5] Imported new chain segment                blocks=1 txs=0 mgas=0.000     elapsed=1.777ms   mgasps=0.000       number=11572925 hash=1fabd3…af66fe cache=1.59mB
INFO[02/13,07:02:26 Z] [35] Commit new mining work                    number=11572926 txs=0 uncles=0 elapsed=458.665µs
INFO[02/13,07:02:27 Z] [14] Committed                                 address=0x1d4E05BB72677cB8fa576149c945b57d13F855e4 hash=60b9aa…94f648 number=11572926
INFO[02/13,07:02:27 Z] [5] Imported new chain segment                blocks=1 txs=0 mgas=0.000     elapsed=1.783ms   mgasps=0.000       number=11572926 hash=60b9aa…94f648 cache=1.59mB
INFO[02/13,07:02:27 Z] [35] Commit new mining work                    number=11572927 txs=0 uncles=0 elapsed=483.436µs
```

## klay console

Klaytn은 console이라는 CLI 클라이언트를 제공한다. CN/PN은 보안상 RPC인터페이스로 console을 열어두지 않기 때문에 IPC 방식으로만 동작중인 CN/PN에 console을 연결할 수 있다.

IPC파일은 CN/PN 의 데이터 디렉토리에 klay.ipc 이름으로 있다.

연결은 다음과 같이 할 수 있다. 

```
$ klay attach /klay_home/data/klay.ipc
Welcome to the Klaytn JavaScript console!

instance: Klaytn/vv0.4.4/linux-amd64/go1.9.4
coinbase: 0x67f68fdd9740fd7a1ac366294f05a3fd8df0ed40
at block: 11573551 (Wed, 13 Feb 2019 07:12:52 UTC)
 datadir: /klay_home/data
 modules: admin:1.0 debug:1.0 istanbul:1.0 klay:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0
 > 
 ```
 
 console에서 사용할 수 있는 명령어는 [Klaytn Document](http://docs.klaytn.net/api/introduction.html)에서 확인할 수 있다. 
 
 여기서는 CN/PN 상태 확인을 위한 몇가지 API를 소개한다. 
 
 - 마지막 블록번호
 - 현재 연결된 Klaytn Node의 수


### 마지막 블록번호

마지막 블록번호를 확인하면 현재 블록을 생성(CN의 경우)하는지 생성된 블록을 잘 받고 있는지(CN,PN)를 확인할 수 있다.

```
> klay.blockNumber
11573819
```

Klaytn은 1초 주기로 블록을 생성하기 때문에 blockNumber가 0이거나 증가하지 않으면 문제가 있는 것이다.
 
### 현재 연결된 Klaytn Node의 수

```
> net.peerCount
14
```

CN의 경우 Core Cell Network의 모든 CN + 연결된 PN의 수가 연결 수로 반환되고 PN의 경우 연결한 CN 수 + 연결된 EN의 수가 반환된다.
