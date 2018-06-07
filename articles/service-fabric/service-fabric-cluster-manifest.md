---
title: Azure Service Fabric 독립 실행형 클러스터 구성 | Microsoft Docs
description: 독립 실행형 또는 온-프레미스 Azure Service Fabric 클러스터를 구성하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 0c5ec720-8f70-40bd-9f86-cd07b84a219d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: e0fed608ac9dd02a6fe5563eefc30edb63d224b1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>독립 실행형 Windows 클러스터에 대한 구성 설정
이 문서에서는 ClusterConfig.json 파일을 사용하여 독립 실행형 Azure Service Fabric 클러스터를 구성하는 방법에 대해 설명합니다. 이 파일을 사용하여 오류 및 업그레이드 도메인 측면에서 네트워크 토폴로지뿐만 아니라 클러스터의 노드, 보안 구성에 관한 정보를 지정합니다.

[독립 실행형 Microsoft Azure Service Fabric 패키지를 다운로드](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)할 때, ClusterConfig.json 샘플도 포함됩니다. 이름에 "DevCluster"가 있는 샘플은 논리 노드를 사용하여 이러한 세 개 노드가 모두 동일한 컴퓨터에 포함된 클러스터를 만듭니다. 이러한 노드 중 하나 이상은 주 노드로 표시되어야 합니다. 이 클러스터 형식은 개발 또는 테스트 환경에 유용합니다. 프로덕션 클러스터로 지원되지는 않습니다. 이름에 MultiMachine이 있는 샘플을 사용하면 각 노드가 별도의 컴퓨터에 있는 프로덕션급 클러스터를 만들 수 있습니다. 이러한 클러스터의 주 노드 수는 클러스터의 [안정성 수준](#reliability)에 따라 결정됩니다. 5.7 릴리스 API 버전 05-2017에서는 안정성 수준 속성을 제거했습니다. 대신 코드에서 클러스터에 가장 최적화된 안정성 수준을 계산합니다. 버전 5.7부터는 이 속성의 값을 설정하지 마세요.


* ClusterConfig.Unsecure.DevCluster.json 및 ClusterConfig.Unsecure.MultiMachine.json은 보안이 유지되지 않는 테스트 또는 프로덕션 클러스터 각각을 만드는 방법을 보여 줍니다.

* ClusterConfig.Windows.DevCluster.json 및 ClusterConfig.Windows.MultiMachine.json은 [Windows 보안](service-fabric-windows-cluster-windows-security.md)을 사용하여 보안이 유지되는 테스트 또는 프로덕션 클러스터를 만드는 방법을 보여 줍니다.

* ClusterConfig.X509.DevCluster.json 및 ClusterConfig.X509.MultiMachine.json은 [X509 인증서 기반 보안](service-fabric-windows-cluster-x509-security.md)을 사용하여 보안이 유지되는 테스트 또는 프로덕션 클러스터를 만드는 방법을 보여 줍니다.

이제 아래와 같이 ClusterConfig.json 파일의 여러 섹션을 살펴보겠습니다.

## <a name="general-cluster-configurations"></a>일반 클러스터 구성
일반 클러스터 구성에는 다음 JSON 코드 조각과 같이 광범위한 클러스터별 구성이 포함됩니다.

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

name 변수에 Service Fabric 클러스터에 친숙한 이름을 할당하여 해당 이름을 지정할 수 있습니다. clusterConfigurationVersion은 클러스터의 버전 번호입니다. Service Fabric 클러스터를 업그레이드할 때마다 늘려줍니다. apiVersion은 기본값으로 그대로 둡니다.

## <a name="nodes-on-the-cluster"></a>클러스터의 노드

    <a id="clusternodes"></a>

다음 코드 조각과 같이 nodes 섹션을 사용하여 Service Fabric 클러스터에서 노드를 구성할 수 있습니다.

    "nodes": [{
        "nodeName": "vm0",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType1",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "localhost",
        "nodeTypeRef": "NodeType2",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],

Service Fabric 클러스터에는 세 개 이상의 노드가 있어야 합니다. 설정에 따라 이 섹션에 노드를 더 추가할 수 있습니다. 다음 표에서는 각 노드의 구성 설정에 대해 설명합니다.

| **노드 구성** | **설명** |
| --- | --- |
| nodeName |노드에 이름을 지정할 수 있습니다. |
| iPAddress |명령 창을 열고 `ipconfig`를 입력하여 노드의 IP 주소를 찾습니다. IPV4 주소를 적어두고 iPAddress 변수에 할당합니다. |
| nodeTypeRef |노드마다 다른 노드 형식을 할당할 수 있습니다. [노드 유형](#node-types) 은 다음 섹션에서 정의됩니다. |
| faultDomain |장애 도메인은 클러스터 관리자가 공유되는 물리적 종속성으로 인해 동시에 장애를 경험할 가능성이 있는 실제 노드를 정의할 수 있도록 합니다. |
| upgradeDomain |업그레이드 도메인은 Service Fabric 업그레이드를 위해 거의 같은 시간에 종료된 노드 집합을 나타냅니다. 물리적 요구 사항에 따라 제한되지 않으므로 할당할 노드 및 해당 노드가 할당되는 업그레이드 도메인을 선택할 수 있습니다. |

## <a name="cluster-properties"></a>클러스터 속성
ClusterConfig.json의 properties 섹션은 다음과 같이 클러스터를 구성하는 데 사용됩니다.

### <a name="reliability"></a>안정성
reliabilityLevel이라는 개념은 클러스터의 주 노드에서 실행될 수 있는 Service Fabric 시스템 서비스의 복사본 또는 인스턴스 수를 정의합니다. 이는 이러한 서비스의 안정성 및 클러스터의 안정성을 결정합니다. 값은 클러스터 생성 및 업그레이드 시 시스템에서 계산됩니다.

    <a id="reliability"></a>

### <a name="diagnostics"></a>진단
다음 코드 조각과 같이 diagnosticsStore 섹션에서 진단 및 문제 해결 노드 또는 클러스터 오류를 사용하도록 매개 변수를 구성할 수 있습니다. 

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
    }

metadata는 클러스터 진단에 대한 설명이며, 설정에 따라 지정될 수 있습니다. 이러한 변수는 성능 카운터뿐만 아니라 ETW 추적 로그 및 크래시 덤프를 수집하는 데에도 유용합니다. ETW 추적 로그에 대한 자세한 내용은 [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) 및 [ETW 추적](https://msdn.microsoft.com/library/ms751538.aspx)을 참조하세요. [크래시 덤프](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) 및 [성능 카운터](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx)를 포함한 모든 로그는 컴퓨터의 connectionString 폴더로 보낼 수 있습니다. 또한 AzureStorage를 사용하여 진단을 저장할 수 있습니다. 다음 샘플 코드 조각을 참조하세요.

    "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "AzureStorage",
        "IsEncrypted": "false",
        "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
    }

### <a name="security"></a>보안
security 섹션은 보안 독립 실행형 Service Fabric 클러스터에 필요합니다. 다음 코드 조각은 이 섹션의 일부를 보여 줍니다.

    "security": {
        "metadata": "This cluster is secured using X509 certificates.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        . . .
    }

metadata는 보안 클러스터에 대한 설명이며, 설정에 따라 지정될 수 있습니다. ClusterCredentialType 및 ServerCredentialType은 클러스터 및 노드에서 구현하는 보안 종류를 결정합니다. 인증서 기반 보안의 경우 *X509*, Azure Active Directory 기반 보안의 경우 *Windows*로 설정할 수 있습니다. security 섹션의 나머지 부분은 보안 종류에 따라 설정됩니다. security 섹션의 나머지 부분을 채우는 방법에 대한 자세한 내용은 [독립 실행형 클러스터의 인증서 기반 보안](service-fabric-windows-cluster-x509-security.md) 또는 [독립 실행형 클러스터의 Windows 보안](service-fabric-windows-cluster-windows-security.md)을 참조하세요.

### <a name="node-types"></a>노드 유형

    <a id="nodetypes"></a>

nodeTypes 섹션에서는 클러스터에서 사용하는 노드 유형에 대해 설명합니다. 아래 코드 조각과 같이 클러스터에 하나 이상의 노드 유형을 지정해야 합니다. 

    "nodeTypes": [{
        "name": "NodeType0",
        "clientConnectionEndpointPort": "19000",
        "clusterConnectionEndpointPort": "19001",
        "leaseDriverEndpointPort": "19002"
        "serviceConnectionEndpointPort": "19003",
        "httpGatewayEndpointPort": "19080",
        "reverseProxyEndpointPort": "19081",
        "applicationPorts": {
            "startPort": "20575",
            "endPort": "20605"
        },
        "ephemeralPorts": {
            "startPort": "20606",
            "endPort": "20861"
        },
        "isPrimary": true
    }]

name은 이 특정 노드 유형에 대한 친숙한 이름입니다. 이 노드 유형의 노드를 만들려면 [앞에서 설명한 대로](#nodes-on-the-cluster) 해당 노드에 대한 nodeTypeRef에 친숙한 이름을 할당합니다. 각 노드 유형에 사용되는 연결 끝점을 정의합니다. 이 클러스터의 다른 끝점과 충돌하지 않는 한 이러한 연결 끝점에 대해 임의의 포트 번호를 선택할 수 있습니다. 다중 노드 클러스터에는 [reliabilityLevel](#reliability)에 따라 하나 이상의 주 노드가 있습니다(즉, isPrimary가 *true*로 설정됨). 주 노드 유형 및 주 노드가 아닌 다른 노드 유형에 대한 자세한 내용은 nodeTypes 및 reliabilityLevel 정보에 대한 [Service Fabric 클러스터 용량 계획 고려 사항](service-fabric-cluster-capacity.md)을 참조하세요. 

#### <a name="endpoints-used-to-configure-the-node-types"></a>노드 형식을 구성하는 데 사용되는 끝점
* clientConnectionEndpointPort는 클라이언트 API를 사용할 때 클라이언트에서 클러스터에 연결하는 데 사용하는 포트입니다. 
* clusterConnectionEndpointPort는 노드 간에 통신하는 포트입니다.
* leaseDriverEndpointPort는 노드가 여전히 활성 상태인지 확인하기 위해 클러스터 임대 드라이버에서 사용하는 포트입니다. 
* serviceConnectionEndpointPort는 특정 노드에서 Service Fabric 클라이언트와 통신하기 위해 해당 노드에 배포된 응용 프로그램과 서비스에서 사용하는 포트입니다.
* httpGatewayEndpointPort는 Service Fabric Explorer에서 클러스터에 연결하는 데 사용하는 포트입니다.
* ephemeralPorts는 [OS에서 사용하는 동적 포트](https://support.microsoft.com/kb/929851)를 재정의합니다. Service Fabric에서는 이러한 포트 중 일부를 응용 프로그램 포트로 사용하고, 나머지 포트는 OS에서 사용할 수 있습니다. 또한 이 범위가 OS에 있는 기존 범위에 매핑되므로 어떤 목적이든 JSON 샘플 파일에 지정된 범위를 사용할 수 있습니다. 시작 포트와 끝 포트 간의 차이가 255 이상인지 확인합니다. 이 범위가 OS와 공유되므로 이 차이가 너무 낮으면 충돌이 발생할 수 있습니다. 구성된 동적 포트 범위를 확인하려면 `netsh int ipv4 show dynamicport tcp`를 실행하세요.
* applicationPorts는 Service Fabric 응용 프로그램에서 사용하는 포트입니다. 응용 프로그램 포트 범위는 응용 프로그램의 끝점 요구 사항을 충족할 수 있을 만큼 충분히 커야 합니다. 이 범위는 컴퓨터의 동적 포트 범위, 즉 구성에 설정된 ephemeralPorts 범위에서 제외해야 합니다. Service Fabric에서는 새 포트가 필요할 때마다 이러한 포트를 사용하여 해당 포트에 대한 방화벽을 엽니다. 
* reverseProxyEndpointPort는 선택적 역방향 프록시 끝점입니다. 자세한 내용은 [Service Fabric 역방향 프록시](service-fabric-reverseproxy.md)를 참조하세요. 

### <a name="log-settings"></a>로그 설정
fabricSettings 섹션에서 Service Fabric 데이터 및 로그에 대한 루트 디렉터리를 설정할 수 있습니다. 이러한 디렉터리는 초기 클러스터를 만드는 동안에만 사용자 지정할 수 있습니다. 이 섹션에 대한 다음 샘플 코드 조각을 참조하세요.

    "fabricSettings": [{
        "name": "Setup",
        "parameters": [{
            "name": "FabricDataRoot",
            "value": "C:\\ProgramData\\SF"
        }, {
            "name": "FabricLogRoot",
            "value": "C:\\ProgramData\\SF\\Log"
    }]

OS 드라이브가 아닌 드라이브를 FabricDataRoot 및 FabricLogRoot로 사용하는 것이 좋습니다. OS에서 응답을 중지하는 상황을 방지하는 데 더 많은 안정성을 제공합니다. 데이터 루트만 사용자 지정하는 경우 로그 루트가 해당 데이터 루트의 한 수준 아래에 배치됩니다.

### <a name="stateful-reliable-services-settings"></a>상태 저장 Reliable Services 설정
KtlLogger 섹션에서 Reliable Services에 대한 전역 구성 설정을 지정할 수 있습니다. 이러한 설정에 대한 자세한 내용은 [상태 저장 Reliable Services 구성](service-fabric-reliable-services-configuration.md)을 참조하세요. 아래 예제에서는 상태 저장 서비스에 대해 신뢰할 수 있는 모든 컬렉션을 백업하기 위해 만드는 공유 트랜잭션 로그를 변경하는 방법을 보여 줍니다.

    "fabricSettings": [{
        "name": "KtlLogger",
        "parameters": [{
            "name": "SharedLogSizeInMB",
            "value": "4096"
        }]
    }]

### <a name="add-on-features"></a>추가 기능
추가 기능을 구성하려면 apiVersion을 04-2017 이상으로 구성하고, addonFeatures를 다음과 같이 구성합니다.

    "apiVersion": "04-2017",
    "properties": {
      "addOnFeatures": [
          "DnsService",
          "RepairManager"
      ]
    }

### <a name="container-support"></a>컨테이너 지원
Windows Server 컨테이너 지원 및 Hyper-V 컨테이너 지원 모두를 독립 실행형 클러스터에 사용하려면 DnsService 추가 기능을 사용해야 합니다.


## <a name="next-steps"></a>다음 단계
독립 실행형 클러스터 설정에 따라 ClusterConfig.json 파일을 완전하게 구성했으면 클러스터를 배포할 수 있습니다. [독립 실행형 Service Fabric 클러스터 만들기](service-fabric-cluster-creation-for-windows-server.md)의 단계를 수행합니다. 그런 다음 [Service Fabric Explorer로 클러스터 시각화](service-fabric-visualizing-your-cluster.md)로 이동하여 해당 단계를 수행합니다.

