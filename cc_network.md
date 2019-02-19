---
# permalink: "cn_bootstrapping/cc_network"
layout: "single"
sidebar:
  nav: "cn_bootstrapping"
---

# 네트워크 구성

Core Cell은 

- 복수 Subnet (추천)
- 단일 Subnet

으로 구성할 수 있다.

## 복수의 Subnet으로 Core Cell 구성

CN과 PN은 일반적인 웹서비스의 DB+AppServer와 Proxy Web Server의 역할과 비슷하다. 따라서 비슷하게 2-layer로 Subnet을 구성하는 것을 추천한다. CN, PN외에 모니터링 서버등 관리용 서버들이 있기 때문에 3개의 Subnet으로 Core Cell을 구성하는 법을 설명한다. 

다음의 Subnet들이 필요하다. 
- CN Subnet
- PN Subnet
- Mgmt Subnet

### CN Subnet
CN Subnet은 Core Cell의 CN 서버들로 구성된다. 1대의 CN으로 구성하지만 HA(High Availability)구성에 따라 최대 2대의 서버로 구성된다. 외부(Internet)를 통해 Core Cell Network(CCN)의 다른 CN들과 연결을 해야하기 때문에 CCN의 모든 CN들의 Ip, Port를 방화벽에서 열어줘야 한다. (이 정보는 Baobab의 운영자에게 받을 수 있다.) Core Cell 내부의 다른 Subnet과의 통신은 PN Subnet의 PN들과 Klaytn P2P 기본 포트(32323)들로 연결이 필요하며 관리 Subnet의 모니터링 서버를 위한 CN 모니터링 포트(61001)과 관리를 위해 SSH(22) 포트를 열어줘야 한다. 

![](/resources/cn_subnet.png)

| Origin Subnet | Target Subnet | Ingress | Egress |
| --- | --- | --- | --- |
| CN Subnet | PN Subnet | P2P: 32323 | All |
| CN Subnet | Mgmt Subnet | SSH: 22, Monitoring: 61001 | All |
| CN Subnet | Public (Internet) | 각 CN의 IP 와 P2P port | All |

### PN Subnet
PN Subnet의 외부 EN들과 연결하여 서비스를 제공하는 PN 서버들의 Subnet이다.

PN Subnet의 연결대상은 다음과 같다.
- Core Cell의 CN
- 다른 Core Cell의 일부 PN
- Core Cell의 관리서버들 (Mgmt, Monitoring)
- EN 노드들

![](/resources/pn_subnet.png)

| Origin Subnet | Target Subnet | Ingress | Egress |
| --- | --- | --- | --- |
| PN Subnet | CN Subnet | P2P: 32323 | All |
| PN Subnet | Mgmt Subnet | SSH: 22, Monitoring: 61001 | All |
| PN Subnet | Public (Internet) | P2P: 32323 | All |

### Mgmt Subnet
Mgmt Subnet은 운영자가 Core Cell의 서버에게 접속(ssh)하기 위한 진입 서브넷이다. VPN 서버를 두어 운영자의 환경에서 연결을 할 수 있도록 해야하며, 모니터링 서버와 Core Cell의 서버들을 관리하기 위한 툴이 설치된 관리용 서버를 두어야 한다.

![](/resources/admin_subnet.png)

| Origin Subnet | Target Subnet | Ingress | Egress |
| --- | --- | --- | --- |
| Mgmt Subnet | CN Subnet | All | All |
| Mgmt Subnet | PN Subnet | All | All |
| Mgmt Subnet | Public Subnet | VPN(tcp): 443, VPN(udp): 1194 | All |


## 단일 Subnet으로 Core Cell 구성

단일 Subnet은 복수의 Subnet 구성이 어렵거나 개발/테스트용 환경 구축에 사용한다.

하나의 CC Subnet에 모든 노드를 두고 아래와 같이 CN은 CNN의 다른 CN들만 P2P(32323) 포트로 연결할 수 있도록 방화벽을 설정한다. PN은 Endpoint Node Network(ENN)의 EN과 Core Cell Network(CNN)의 PN과 연결을 할 있도록 P2P 포트를 개방한다. 추가적으로 선택적으로 원격 관리를 위한 VPN과 모니터링 서버를 구성한다.

![](/resources/cc_single_subnet.png)
