---
# permalink: "cn_bootstrapping/cc_installation"
layout: "single"
sidebar:
  nav: "cn_bootstrapping"
---
# 설치

## CN 설치

### Linux

리눅스에 CN을 설치하는 과정은 다음과 같다.

1. Klaytn 패키지 다운로드
1. 압축 해제
1. CN 설정
1. CN 시작

Klaytn 리눅스 패키지는 실행에 필요한 바이너리와 설정파일을 모아둔 압축파일이고 다음과 같이 구성되어 있다. 
```
- bin
  |- klay
  |- klayd
- conf
  |- klay.conf
```

| File Name | File Description |
| --- | --- |
| bin/klay | CN 실행 파일 |
| bin/klayd | CN 시작/종료 스크립트 파일 |
| conf/klay.conf | CN 설정 파일 |

#### Klaytn 패키지 다운로드

다음의 경로에서 Klaytn 최신 패키지(klaytn-vX.X.X-linux-amd64.tar.gz)를 받는다. 

[Download Latest Klaytn](/releases/klaytn-latest/)

또는 아래의 명령어로 다운로드를 할 수 있다. 

```
$ wget https://s3.ap-northeast-2.amazonaws.com/klaytn-ops-stuff/releases/klaytn/latest/klay-v0.4.4-9-linux-amd64.tar.gz
```

#### Klaytn 설치

설치는 다운로드한 패키지 압축파일을 압축해제를 하면된다.

```
$ tar zxf klay-v0.4.4-9-linux-amd64.tar.gz
```

## CN 설정

CN 설정은 CN의 데이터를 저장할 Data 디렉토리를 생성하고 klay.conf의 몇가지 설정값을 지정하는 것으로 이루어져있다.

1. CN 데이터 디렉토리 생성
1. CN 노드키 & CN URI 생성
1. CN klay.conf 설정

### CN Data 디렉토리 생성

CN의 Data디렉토리는 지속적으로 증가한는 Klaytn 블록체인 데이터를 저장하는 곳으로 충분히 큰 파티션을 가진 저장장소로 지정하는 것을 추천한다.

```
$ mkdir /data/klay
```

### CN 노드키 & 노드URI 생성

CN 노드키와 노드URI는 최초 한번만 생성을 하면 되고 노드URI 정보는 Core Cell Network의 다른 Core Cell에게 공유를 해야 한다. 이 노드URI를 이용해 CN들끼리 연결을 할 수 있기 때문이다. 노드URI는 CN 노드키 기반으로 생성이 되기 때문에 klay 명령어를 이용해 동시에 생성을 할 수 있다. 

```klay gennodekey``` 명령어를 이용하면 노드키와 노드URI가 포함된 node_info.json 파일을 생성할 수 있다.

```
$ klay gennodekey --file
$ ls
nodekey node_info.json
```

nodekey 는 64길이의 hex값으로 된 Private Key이다. CN에서 내부적으로 사용하는 개인키이다. CN이 설치된 서버의 Klaytn Data 디렉토리에 두어야 하며 분실되지 않도록 주의해야 한다. 

```
$ cat nodekey
f08f2118c455a6c9c9b5e035d3571e570a719ea61771e268546e796a264acc2b
$ mv nodekey /data/klay/
```

생성한 node_info.json는 다음의 내용을 포함한다.

| Key Name | Description | Example |
| -- | --- | --- |
| NodeAddress | 노드주소 | 0xc8a23d67f2471066fa1b07270651fea7e5c0cf78 |
| NodeKey | 노드키 | aaa7248dfdf19418ae9121a0f39db39c5c27a3e404ea7c1b8e020ca8dbe7e71a |
| NodeURI | 노드URI | kni://4f2f47f3bf35a2c576d3345e6e9c49b147d510c05832d2458709f63c3c90c76ead205975d944ed65e77dd4c6f63ebe1ef21d60da95952bc1e200e7487f4d9e1b@0.0.0.0:32323?discport=0 |

```klay gennodeky``` 명령어로 생성한 node_info.json 파일은 위의 노드 정보를 json행태로 포함하고 있다.
```
$ cat node_info.json
{
	"NodeAddress": "0xc8a23d67f2471066fa1b07270651fea7e5c0cf78",
	"NodeKey": "aaa7248dfdf19418ae9121a0f39db39c5c27a3e404ea7c1b8e020ca8dbe7e71a",
	"NodeURI": "kni://4f2f47f3bf35a2c576d3345e6e9c49b147d510c05832d2458709f63c3c90c76ead205975d944ed65e77dd4c6f63ebe1ef21d60da95952bc1e200e7487f4d9e1b@0.0.0.0:32323?discport=0"
}
```

#### 노드URI 등록

Core Cell Network(CCN)의 허가된 일원으로 참여하기 위해서 위에서 생성한 노드URI를 등록해야 한다. 등록은 다음의 과정으로 한다.

1. klay gennodekey로 노드URI를 생성한다. (node_info.json)
1. node_info.json내의 NodeURI의 Host, Port 정보를 자신의 CN Public IP와 설정한 P2P Port 정보로 대체한다. 
1. baobab_op@klaytn.net 으로 수정한 노드URI를 양식에 맞춰 보낸다. 

위의 과정에서 생성한 노드URI의 Host는 0.0.0.0, 포트는 32323의 기본값으로 설정이되어 있다. CN의 기본 P2P 포트값을 사용하고 CN의 public IP가 123.456.789.012라면 아래의 예와 같이 노드URI를 생성할 수 있다. 

```
kni://4f2f47f3bf35a2c576d3345e6e9c49b147d510c05832d2458709f63c3c90c76ead205975d944ed65e77dd4c6f63ebe1ef21d60da95952bc1e200e7487f4d9e1b@123.456.789.012:32323?discport=0
```

위의 노드URI 정보를 포함한 CN의 등록 정보를 baobab_op@klaytn.net 으로 보낸다. 양식은 아래와 같다. 

```
업체명 : Kakao
CN URI : kni://4f2f47f3bf35a2c576d3345e6e9c49b147d510c05832d2458709f63c3c90c76ead205975d944ed65e77dd4c6f63ebe1ef21d60da95952bc1e200e7487f4d9e1b@123.456.789.012:32323?discport=0
```

#### CN 설정

CN 설정은 다음의 순서로 한다. 

1. 초기화에 필요한 파일을 CN 데이터 디렉토리에 복사
1. klay.conf 설정

##### 초기화 파일 복사

CN을 초기화하려면 다음의 파일이 필요하다. 
- nodekey
- static-nodes.json
- genesis.json

nodekey는 이전 과정에서 생성한 파일이고, static-nodes.json, genesis.json 파일은 모든 Core Cell이 노드URI를 등록한 후 baobab 운영자로 부터 받을 수 있다. 

nodekey와 static-nodes.json 파일은 CN 데이터 디렉토리에 복사한다. 

```
$ cp nodekey /data/klay/
$ cp static-nodes.json /data/klay/
```

##### 제네시스 블록 생성

CN을 시작하기에 앞서 baobab 테스트넷 첫번째 블록을 초기화해야 하고 klay 명령어와 baobab의 genesis.json 파일이 필요하다. 

```
$ klay init --datadir /data/klay genesis.json
...
```

이로써 CN을 기동할 수 있는 준비가 모두 완료되었다.

##### klay.conf 설정

klay.conf에 정의된 속성은 다음과 같다.

| Name | Description | Example |
| --- | --- | --- |
| NETWORK_ID | 클레이튼 네트워크 아이디 | 1 : 메인넷, 1000 : 아스펜 테스트넷, 1001 : 바오밥 테스트넷 |
| RPC_PORT | RPC 포트, Klaytn RPC 클리이언트(ex klaytn console)용 포트 | 기본값 : 8551 |
| WS_PORT | WS 포트, Klaytn WS 클라이언트용 포트 | 기본값 : 8552 |
| PORT | P2P 포트, Klaytn 노드들 끼리 연결할 사용하는 포트 | 기본값 : 32323 |
| KLAY_HOME | Klaytn 노드 홈 디렉토리 | 기본값 : ~/.klay |
| DATA_DIR | Klaytn의 블록체인 데이터가 기록용 디렉토리 | /klay_home/data |
| LOG_DIR | Klaytn 노드의 로그가 기록용 디렉토리 | /klay_home/logs |
| NODE_TYPE | Klaytn 노드의 타입 | CN, PN, EN |

아래는 Baobab(Network ID : 1001)의 CCN에 참여하는 CN의 설정예이다. 기본 포트를 사용하고 /klay_home 디렉토리에 대용량 파티션을 마운팅했다고 가정한다.
```
# Configuration file for the klay service.

NETWORK_ID=1001

RPC_PORT=8551
WS_PORT=8552
PORT=32323

KLAY_HOME=/klay_home

DATA_DIR=$KLAY_HOME/data
LOG_DIR=$KLAY_HOME/logs

# NODE_TYPE [CN,PN,EN]
NODE_TYPE=CN
```

#### CN 시작

다음의 명령어로 CN을 제어할 수 있다.

**시작**
```
$ klayd start
```

**종료**
```
$ klayd stop
```

**동작상태확인**
```
$ klayd status
```


### RPM 설치 (Redhat/CentOS/Fedora)

RPM설치과정은 다음과 같다.

1. 최신버전의 CN설치용 RPM 다운로드
1. RPM 설치 
1. CN 설정

#### Klaytn RPM 다운로드

다음의 경로에서 Klaytn 최신 패키지(klaytn-vX.X.X.el7.x86_64.rpm)를 받는다. 

[Download Latest Klaytn](/releases/klaytn-latest/)

또는 아래의 명령어로 다운로드를 할 수 있다. 

```
$ wget https://s3.ap-northeast-2.amazonaws.com/klaytn-ops-stuff/releases/klaytn/latest/klaytn-v0.4.4-9.el7.x86_64.rpm
```

#### RPM 설치

yum 명령어로 다음과 같이 설치한다. 

```
$ yum install klaytn-v0.4.4-9.el7.x86_64.rpm
```

#### CN 설정

CN 설정은 패키지 설치와 동일하다. 단 패키지 설치시 관련 파일(실행, 설정 파일)들이 모두 패키지내에 있는 것에 반해 RPM 설치는 다음의 경로에 실행/설치파일들이 위치한다.

| File Name | Location |
| --- | --- |
| klay | /usr/local/bin/klay |
| klay.conf | /etc/klay/conf/klay.conf |

#### CN 시작

systemctl 리눅스 명령으로 CN을 제어할 수 있다.

**시작**
```
$ systemctl start klay.service
```

**종료**
```
$ systemctl stop klay.service
```

**동작상태확인**
```
$ systemctl status klay.service
```

## PN 설치/설정

PN의 설치는 CN의 설치와 거의 비슷하지만 PN이 연결할 CN목록이 있는 static-node.json 파일과 klay.conf 설정이 다르다.

Baobab 운영자로 부터 static-node.json을 받은 CN과 달리 PN의 static-node.json파일은 직접 만들어야 한다. PN의 static-node.json은 PN이 연결할 CN 목록이 정의되어 있고 보통은 자신의 Core Cell의 CN으로 한정된다.

**static-nodes.json**
```
{
  kni://4f2f47f3bf35a2c576d3345e6e9c49b147d510c05832d2458709f63c3c90c76ead205975d944ed65e77dd4c6f63ebe1ef21d60da95952bc1e200e7487f4d9e1b@10.11.2.101:32323?discport=0 
}
```
CN의 노드URI은 CN 설치에서 생성한 값으로 이용한다. 단 Host IP는 CN의 내부 IP로 치환하여 사용한다. (주의 : CN 등록때 Public IP를 사용한 것과 다르다.)

**klay.conf**

```
# Configuration file for the klay service.

NETWORK_ID=1001

RPC_PORT=8551
WS_PORT=8552
PORT=32323

KLAY_HOME=/klay_home

DATA_DIR=$KLAY_HOME/data
LOG_DIR=$KLAY_HOME/logs

# NODE_TYPE [CN,PN,EN]
NODE_TYPE=PN
```

klay.conf 는 NODE_TYPE만 PN으로 설정하고 나머지는 CN과 동일하다.

### PN 시작

PN 시작은 CN과 동일하다. CN 시작을 참조하라.

