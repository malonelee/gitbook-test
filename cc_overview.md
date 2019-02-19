---
# permalink: "cn_bootstrapping/cc_overview"
layout: "single"
sidebar:
  nav: "cn_bootstrapping"
---
# Overview

Klaytn은 다 수의 컴퓨터가 p2p 네트워크로 연결되어 블록체인 서비스를 제공할 수 있게 하는 플랫폼 소프트웨어다. 이 문서의 목적은 Klaytn Network의 주요 구성요소인 Core Cell(CC)을 설치하고 운영할 수 있게 하는 것이다.

Klaytn Core Cell을 알아보기 전에 Klaytn Network의 구성요소에 대해서 먼저 살펴보자.
Klaytn Network은 다음의 요소로 구성되어 있다.

**<그림 1> Klaytn Network**
![<그림 1> Klaytn Network](/resources/klaytn_network_overview.png)


| Name | Description |
| --- | --- |
| Core Cell Network | 트랜잭션을 검증 및 실행하여 **블록을 만들고** 전파하는 역할을 하는 노드들로 구성된 네트워크이다. |
| Endpoint Node Network | 트랜잭션을 생성하고 RPC API 요청을 받아들이거나 서비스체인의 데이터 저장 트랜잭션을 받아들이는 역할을 하는 노드들, 즉 Endpoint Node (EN)들로 구성된 네트워크이다. |
| ServiceChain Network | 서비스체인별 네트워크로 서비스 운영자가 Klaytn 노드들을 이용해 메인체인과는 별개의 블록체인을 생성하기 위해 구성한 네트워크이다. 메인체인의 EN에 연결되어 메인체인으로 데이터 저장 트랜잭션 등을 보낼 수 있다. |

위의 설명에서 볼 수 있듯이 Core Cell Network(CCN)은 블록체인의 블록을 생성할 수 있는 Core Cell(CC)들의 모임이다. CC는 1대 이상의 노드로 구성되어 있고 그림2는 노드 단위로 그림1을 상세하게 표현한 것이다.

**<그림 2> Klaytn Network with Nodes**   
![<그림 2> Detailed Klaytn Network](/resources/klaytn_network_node.png)


CC는 간략하게 다음의 순서로 설치/운영을 시작할 수 있다.

1. H/W를 베어메탈 또는 클라우드 VM로 준비한다.
2. 권장하는 형태로 Subnet 네트워크 구성을 한다.
3. Klaytn S/W를 설치 및 설정을 한다.
4. Klaytn S/W 서비스를 시작한다.
