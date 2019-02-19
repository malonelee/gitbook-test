---
# permalink: "cn_bootstrapping/hw"
layout: "single"
sidebar:
  nav: "cn_bootstrapping"
---
# H/W 준비
Klaytn Core Cell(CC)을 아래의 노드들로 구성된다.

- Klaytn CN
- Klaytn PN

**<그림 1> Core Cell 구성도**   
![](/resources/cn_set.png)

| 이름 | 설명 | 네트워크 보안 | 수량 |
| --- | --- | --- |
| Klaytn CN | Core Cell Network의 다른 CN들과 함께 새로운 블록을 생성하는 노드 | 허가된 CN들로만 Network 구성한다. (IP 접근 제어 필요) | 1대 |
| Klaytn PN | {::nomarkdown}<li>Klaytn Endpoint Node Network으로 부터 새로운 tx를 받아 CN에게 전달하고 CN으로부터 받은 새로운 블록을 Klaytn Endpoint Node Network로 전파하는 노드.</li><li>Klaytn Endpoint Node Network의 EN의 증가에 따라 수평적 확장이 필요한 노드들이다.</li>{:/} | {::nomarkdown}<li>CN들과 내부적으로 연결을 하고 외부(인터넷)의 모든 Klaytn 노드들에게 IP/Port를 공개한다.</li><li>PN 부트노드(미구현)를 통해 다른 CN들의 PN의 일부와 연결을 맺는다.</li><li>EN 부트노드(미구현)을 통해 EN에게 연결을 제공한다.</li>{:/} | 1대 이상, 가용성을 위해 2대 이상 추천 | 

Core Cell 은 1개의 CN과 2개 이상의 PN으로 구성하는 것을 추천한다. CN과 연결할 수 있는 노드는 Core Cell Network의 CN들과 Core Cell 내의 PN들 뿐이다. PN은 Klaytn Endpoint Node Network의 모든 EN들과 연결을 맺을 수 있다.

다음은 Core Cell의 노드들을 운영하기에 적합한 H/W 스펙에 대해 설명한다.

## Klaytn H/W 권장 사양
Klaytn Core Cell은 가장 낮은 성능의 서버로 네트워크 전체 성능이 결정되고 블록체인의 특성상 단일 네트워크의 성능향상은 수직적으로만(H/W 성능 향상) 가능함으로 가능한 최고 성능의 서버들로 동일하게 네트워크를 구성하는 것이 좋다.

다음은 권장하는 서버 스펙이다. (CN, PN 동일)
### Bare-metal Server
Intel® Server Product R2312WFTZS<br>
Xeon 20-Core/40T 2.40Ghz(6148)2EA, 256GB(32GB * 8), 12TB(1.92TB SSD * 6, RAID 5)

위의 비슷한 사양의 베어메탈 서버를 준비한다.
