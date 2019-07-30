---

copyright:
  years: 2017, 2019
lastupdated: "2019-07-10"

keywords: IBM Blockchain Platform, IBM Cloud Private, AWS, Data residency, world state

subcollection: blockchain

---

{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}
{:external: target="_blank" .external}

# 데이터 상주
{: #console-icp-about-data-residency}

블록체인 네트워크는 처리되는 데이터 유형을 감지하지 못하기 때문에 특정 종류의 데이터를 보호하기 위해 때로는 추가 단계를 수행해야 합니다. 데이터 상주의 가장 일반적인 요구사항은 특정 국가의 법률과 관련되어 있습니다. 해당 법률에 따르면 IT 시스템에서 처리하고 저장하는 모든 데이터는 특정 국가 내에 유지해야 합니다. 마찬가지로 정부, 보건, 금융 서비스와 같이 고도로 규제된 일부 업계에서는 데이터를 완전히 방화벽 뒤에 보관해야 합니다.

블록체인 네트워크를 통해 여러 조직에서 분산 원장을 사용하여 신뢰할 수 있고 안전한 방식으로 거래하고 데이터를 공유할 수 있습니다. 그러나 이는 데이터가 네트워크의 노드와 해당 노드가 상주하는 지역에 분산될 수 있음을 의미합니다. 조직은 나머지 네트워크에서 데이터를 분리하고 데이터 상주를 달성하기 위한 여러 가지 옵션을 사용할 수 있습니다. 
1. [공유 채널에 있는 개인용 데이터 콜렉션](#console-icp-about-data-residency-fabric)
2. [별도의 채널에 있는 개인용 데이터 콜렉션](#console-icp-about-data-residency-use-case)
3. [한 국가 내에 채널의 모든 노드가 있는 별도의 채널](#console-icp-about-data-residency-use-case-channel)

각 접근법은 사용자 데이터에 대한 향상된 격리 레벨과 보호 레벨을 제공하지만 구현하고 관리하기 위한 추가 노력이 필요합니다. 각 옵션에서 데이터 상주를 달성하기 위한 각 옵션의 사용 방법을 이해하는 데 도움이 되도록 Hyperledger Fabric 네트워크 내에서 데이터를 공유하는 방법의 개요를 제공합니다. 그런 다음 유스 케이스 예를 제공하여 블록체인 컨소시엄 내의 조직이 데이터를 분리하고 지역을 벗어나지 못하도록 각 옵션을 사용하는 방법을 설명합니다. 

## {{site.data.keyword.blockchainfull_notm}} Platform 네트워크 내에서 데이터를 공유하는 방법
{: #console-icp-about-data-residency-fabric}

{{site.data.keyword.blockchainfull_notm}} Platform의 기초가 되는 Hyperledger Fabric의 아키텍처는 순서 지정 서비스(순서 지정자로 구성됨), 인증 기관(CA) 및 피어라는 세 가지 핵심 컴포넌트를 중심으로 집중되어 있습니다. 추가적으로, 조직은 [Fabric SDK](https://hyperledger-fabric.readthedocs.io/en/release-1.4/getting_started.html){: external}를 사용하여 클라이언트 애플리케이션의 이 노드로 트랜잭션을 보냅니다. 데이터 상주를 고려할 때 이 컴포넌트가 데이터와 상호작용하고 데이터를 저장하는 방법을 이해하는 것은 중요합니다. 

**피어**는 블록체인 [원장](https://hyperledger-fabric.readthedocs.io/en/release-1.4/ledger/ledger.html){: external}을 저장하기 위해 컨소시엄의 멤버가 사용합니다. 블록체인 원장은 두 개의 파트로 구성되어 있습니다. 첫 번째 파트는 키-값 쌍의 원장에 모든 데이터의 최신 값을 저장하는 세계 상태입니다. 두 번째 파트는 모든 트랜잭션의 블록체인 레코드입니다. 피어에서는 순서 지정 서비스로부터 새 블록의 양식으로 된 상태 업데이트를 수신합니다. 그런 다음 블록 및 세계 상태를 사용하여 트랜잭션을 확인(또는 커미트)하고, 세계 상태를 업데이트하고, 블록체인의 트랜잭션 로그를 추가합니다. 순서 지정 서비스는 [컨소시엄](/docs/services/blockchain?topic=blockchain-glossary#glossary-consortium)에서 모든 피어에 대한 트랜잭션의 순서를 설정하고 원장의 블록체인 부분에 대한 사본을 저장합니다. 

**채널**은 네트워크 내의 데이터를 전송하기 위한 메커니즘입니다. 채널에 가입하지 않으면 블록체인 네트워크에 참여할 수 없습니다. 채널은 네트워크의 멤버를 사용하여 비즈니스 애플리케이션 간의 논리적 분리를 작성할 수 있으며 트래픽을 제한하여 성능을 향상시킬 수도 있습니다. 또한 안전하게 거래하고 데이터를 분리하기 위해 채널이 컨소시엄 내 조직의 서브넷에서 사용될 수 있습니다.

피어는 가입하는 각 채널마다 별도의 원장을 유지보수합니다. 채널의 멤버인 조직만 피어를 채널에 가입시키고 순서 지정 서비스로부터 원장 업데이트를 수신할 수 있습니다. 결과적으로 각 채널은 유지보수하는 모든 채널 원장의 블록체인 부분을 저장하는 순서 지정 서비스에 바인드됩니다. 클라이언트 애플리케이션에서는 트랜잭션을 지정된 채널의 피어와 순서 지정 서비스에 보냅니다. 이 트랜잭션은 블록체인 내의 트랜잭션 로그에 추가되고 세계 상태로 키-값 쌍에 추가되는 [읽기-쓰기 세트](https://hyperledger-fabric.readthedocs.io/en/release-1.4/readwrite.html){: external}를 포함합니다. 

국가 내 데이터 상주가 요구사항인 경우 순서 지정자의 위치, 순서 지정 서비스 및 클라이언트 애플리케이션을 고려해야 합니다. 또한 채널의 기타 조직에 속하는 피어의 위치도 알고 있어야 합니다. {{site.data.keyword.blockchainfull_notm}} Platform for {{site.data.keyword.cloud_notm}}을 사용하는 경우 사용자와 컨소시엄의 멤버가 컴포넌트를 배치할 수 있는 [{{site.data.keyword.blockchainfull_notm}} Platform 지역 및 위치](/docs/services/blockchain/reference?topic=blockchain-ibp-regions-locations#ibp-regions-locations)의 목록을 찾을 수 있습니다. 

## 데이터 상주에 대한 유스 케이스
{: #console-icp-about-data-residency-use-case}

컨소시엄 예를 사용하여 데이터가 {{site.data.keyword.blockchainfull_notm}} Platform에 분배되는 방법을 설명하고 멤버가 데이터 상주를 달성하는 방법을 탐색할 수 있습니다. 아래의 그림에는 하나의 순서 지정 서비스와 네 개의 조직이 있는 컨소시엄이 포함되어 있습니다. 각 조직에는 하나의 피어 노드가 있습니다. 조직 A 및 조직 B의 두 조직과 순서 지정 서비스는 미국에 위치합니다. 다른 조직인 조직 C 및 조직 D는 독일에 위치합니다. 네 개의 모든 조직은 채널 X의 멤버이며 피어를 채널 X에 가입시켰습니다. 

![컨소시엄 예](images/data_res_use_case.svg "컨소시엄 예")

채널 X에 가입한 각 피어는 채널 원장의 사본을 저장하며, 이는 **그림 1**에 원장 X로 표시되어 있습니다. 미국 및 독일의 피어가 채널에 가입되어 있으므로 채널 원장의 데이터가 두 위치 모두에 상주합니다. 원장의 블록체인 부분도 미국에 위치한 순서 지정 서비스를 통해 저장됩니다. 

컨소시엄의 두 조직에서 두 번째 채널인 채널 Y를 작성하는 경우 두 번째 원장이 작성되고 채널 멤버의 피어에 저장됩니다. 채널에 가입한 조직에만 채널 데이터의 사본이 있습니다. 

![두 번째 채널 추가](images/data_res_use_case_channel.svg "두 번째 채널 추가")


**그림 2**에서는 조직 B 및 조직 D가 채널 Y에 가입했습니다. 조직 B 및 조직 D의 피어가 이제 원장 Y와 원장 X의 사본을 저장합니다. 동일한 순서 지정 서비스가 채널 X 및 채널 Y를 작성하는 데 사용되므로 순서 지정 서비스에는 이제 두 채널 원장의 블록체인 부분의 사본이 있습니다. **그림 1** 및 **그림 2**에서는 독일에서 애플리케이션으로 작성된 데이터가 미국에 저장되며, 이는 데이터 상주가 필요한 경우에는 바람직하지 않습니다. 

위의 예를 사용하여 조직에서 데이터 상주를 달성해야 하는 옵션을 탐색할 수 있습니다. 독일의 규정에 따라 조직 C 및 조직 D에서 작성된 데이터 중 일부가 국가 내에 남아 있어야 한다고 가정하십시오. 독일의 조직에서는 데이터가 미국에 저장되지 않도록 세 가지 옵션을 모두 사용할 수 있습니다. 

## 옵션 1: 공유 채널에 있는 개인용 데이터 콜렉션
{: #console-icp-about-data-residency-use-case-private-data}

조직 C 및 조직 D는 Hyperledger Fabric의 [개인용 데이터 기능](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#what-is-a-private-data-collection "개인용 데이터 콜렉션의 개념"){: external}을 사용하여 데이터를 채널의 모든 조직에 배치되지 않도록 할 수 있습니다. 개인용 데이터 콜렉션을 사용하여 조직이 상태 데이터 피어 투 피어를 콜렉션 읽기 권한이 있는 기타 조직과 (Gossip 프로토콜을 통해) 공유할 수 있습니다. 데이터는 피어의 개별적인 개인 데이터베이스에 저장됩니다.  순서 지정 서비스는 관련되지 않으며 개인용 데이터를 볼 수 없습니다. 콜렉션에서 데이터의 해시만 채널 원장에 추가되고 기타 채널 멤버의 피어 및 순서 지정 서비스에 저장됩니다. 이를 통해 조직이 트랜잭션 세부사항을 공개하려는 경우 개인용 데이터를 확인할 수 있습니다. 자세한 정보는 Fabric 문서에서 [개인용 데이터](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private-data/private-data.html#private-data "개인용 데이터"){: external} 개념에 대한 기사를 참조하십시오. 

![개인용 데이터](images/data_res_private_data.svg "캡션 3")

**그림 3**에서는 조직이 조직 A, 조직 B 또는 순서 지정 서비스와 데이터를 공유하지 않고도 거래할 수 있도록 조직 C 및 조직 D가 개인용 데이터 콜렉션인 OrgC-OrgD 콜렉션을 작성했습니다. 키 값은 이 콜렉션 내의 데이터가 조직 C 및 조직 D의 피어에만 저장되며 독일에 남아 있음을 보여줍니다. 그러나 콜렉션 내 데이터의 해시가 원장 X에 저장되고 광범위한 채널과 공유됩니다. 이는 OrgC-OrgD 콜렉션에서 데이터의 해시가 미국의 피어 및 순서 지정 서비스에 저장됨을 의미합니다. 

개인용 데이터를 고려할 때 **해시된 데이터**와 **암호화된 데이터** 간의 차이점을 이해하는 것이 중요합니다. 암호화에서는 원래 값을 숨기는 양식으로 데이터를 보내기 위해 양방향 기능을 사용하지만 원래의 상태로 다시 변환될 수 없습니다. 예를 들어, 데이터가 TLS를 사용하여 보호되는 네트워크를 통해 전송되면 데이터가 TLS 인증서를 사용하여 암호화됩니다. 그런 다음 암호화 텍스트로 네트워크를 통해 전송된 후 수신인이 복호화합니다. 암호화된 텍스트에는 원래의 모든 데이터가 포함되며 개인 키를 사용하여 복호화할 수 있습니다. 그러나 해시는 숫자 및 문자의 고유한 문자열을 작성하도록 데이터를 사용하는 단방향 기능입니다. 해시된 데이터는 해시를 사용하여 원래의 양식으로 다시 변환될 수 없습니다. 해시를 작성한 데이터를 확인하려면 수신인은 동일한 해시 기능을 사용하여 원래 데이터의 새 해시를 작성하고 해시 값이 일치하는지 확인해야 합니다. 서드파티는 원래 데이터의 사본 없이 해시를 사용할 수 없습니다.   

이 옵션 사용 시 실제 원장 데이터가 해시됨에 따라 조직 A 및 조직 B가 이 데이터를 볼 수 없지만 계속해서 조직 C 및 D가 거래 중임을 볼 수 있고 해당 조직에서 발생하는 트랜잭션의 볼륨을 볼 수 있음을 알고 있어야 합니다. 

또한 개인용 데이터 콜렉션 내부의 데이터가 이를 저장하는 피어에서 영구 제거될 수 있음을 고려하십시오. 데이터가 영구적으로 채널에 저장되는 동안 [개인용 데이터가 영구 제거되기](https://hyperledger-fabric.readthedocs.io/en/release-1.4/private_data_tutorial.html#pd-purge){: external} 전에 콜렉션을 사용하여 멤버가 채널에 커미트되는 블록 수를 지정할 수 있습니다. 데이터가 개인용 데이터 콜렉션에서 제거되면 작성했던 트랜잭션을 확인하는 데 더 이상 채널의 해시를 사용할 수 없습니다. **그림 3**의 네트워크 예에서는 조직 C 및 조직 D가 `block to live` 정책을 사용하여 영구적으로 지속될 필요가 없는 데이터가 지정된 기간 내에 네트워크에서 완전히 제거되었는지 확인할 수 있습니다. 

## 옵션 2: 별도의 채널에 있는 개인용 데이터 콜렉션
{: #console-icp-about-data-residency-use-case-private-data-channel}

조직 C 및 조직 D에서는 별도의 채널에 있는 컨텍스트에서 개인용 데이터 콜렉션을 사용하여 데이터를 위한 추가 격리를 제공할 수도 있습니다. 새 채널(이 경우에는 채널 Y)을 작성하는 경우 개인용 데이터의 해시가 컨소시엄의 기타 멤버와 공유되어 피어에 저장되지 않고도 순서 지정 서비스와만 공유되는지 확인하십시오.

![별도의 채널에서 개인용 데이터 사용](images/data_res_private_data_channel.svg "별도의 채널에서 개인용 데이터 사용")

**그림 4**에서는 미국의 멤버를 포함하지 않는 새 채널인 채널 Y를 구성했습니다. 결과적으로, OrgC-OrgD 콜렉션의 데이터의 해시는 원장 X 대신 원장 Y에 저장되며 미국의 피어에 저장되지 않습니다. 순서 지정 서비스가 미국에 위치하므로 독일에서 작성된 데이터의 해시는 여전히 해당 국가에 남아 있지 않습니다. 

별도의 채널을 작성하면 컨소시엄의 기타 조직과 트랜잭션 세부사항을 공유하지 않을 수 있습니다. 독일의 조직이 공유 채널을 사용한 경우 조직 A 및 조직 B가 개인용 데이터 콜렉션을 통해 채널 원장에 커미트된 트랜잭션 해시의 수를 볼 수 있습니다. 이를 통해 조직은 조직 C 및 조직 D가 거래 중이라는 사실과 해당 조직 간에 생성된 트랜잭션의 볼륨을 파악할 수 있습니다. 새 채널을 구성하려면 작성하고 업데이트할 추가 관리 오버헤드가 필요하다는 점을 고려하십시오. 새 채널을 작성하면 조직 C 및 조직 D가 데이터를 조직 A 및 조직 B와 공유하기가 더 어려워집니다. 

## 옵션 3: 한 국가 내에 모든 컴포넌트가 있는 채널
{: #console-icp-about-data-residency-use-case-channel}

조직 C 및 조직 D는 동일한 국가 내의 모든 인프라를 사용하여 채널을 작성할 수도 있습니다. 이를 위해서는 채널, 애플리케이션 및 순서 지정 서비스에 가입된 피어가 모두 동일한 지역에 상주해야 합니다. 이 시나리오에서는 채널 원장에 저장된 모든 데이터가 해당 지역에 남게 되고 국가 밖에 저장되지 않습니다. 

![별도의 채널 작성](images/data_res_separate_channel.svg "별도의 채널 작성")

**그림 5**에서는 조직 C 및 조직 D가 독일에 남아 있어야 하는 데이터에 대한 새 채널을 작성했습니다. 이를 위해서는 독일에 위치한 새 순서 지정 서비스를 작성하여 채널 원장의 순서 지정자 사본이 국가에 저장되어 있는지 확인해야 합니다. 이 경우 순서 지정자 서비스인 OrgC-peer 및 OrgD-peer가 독일에 위치하므로, 이제 조직 C 및 조직 D가 채널에 데이터를 공개하거나(선택하는 경우) 순서 지정 서비스에서 전체 트랜잭션 데이터를 저장하지 않도록 개인용 데이터 콜렉션을 계속해서 사용할 것을 결정할 수 있습니다. 

한 국가 내에 모든 컴포넌트가 있는 채널을 작성하면 키 값 쌍, 블록체인 트랜잭션 로그 및 개인용 데이터의 해시를 포함한 모든 데이터가 한 지역에 상주합니다. 그러나 이 옵션을 사용하려면 새 채널을 유지보수하기 위한 오버헤드와 순서 지정 서비스 유지보수와 연관된 비용이 필요합니다. 

## 참고 자료
{: #console-icp-about-data-residency-reference}

{{site.data.keyword.blockchainfull_notm}} Platform 네트워크의 데이터 플로우에 대한 자세한 정보는 [트랜잭션 플로우에 대한 Fabric 문서](https://hyperledger-fabric.readthedocs.io/en/release-1.4/txflow.html){: external}를 참조하십시오.

향후에는 Zero Knowledge Proof를 통해 Hyperledger Fabric에서 추가적인 데이터 상주를 달성하도록 기능을 개선할 예정입니다. ZKP(Zero-Knowledge Proof)를 사용하면 “승인자”가 시크릿 자체를 노출하지 않고 시크릿을 알고 있음을 “확인자”에게 보증할 수 있습니다. 이 방식을 사용하면 알고 있는 내용을 표시하지 않고도 규정이 충족됨을 알고 있다고 표시할 수 있습니다.

개인용 데이터 콜렉션 및 Zero Knowledge Proof에 대한 자세한 정보는 [Hyperledger Fabric을 사용하는 개인 및 기밀 트랜잭션](https://developer.ibm.com/tutorials/cl-blockchain-private-confidential-transactions-hyperledger-fabric-zero-knowledge-proof/){: external}에 대한 백서에서 확인할 수 있습니다.