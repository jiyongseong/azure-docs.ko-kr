---
title: Azure Service Fabric 클러스터 노드에 원격 연결 | Microsoft Docs
description: 확장 집합 인스턴스(Service Fabric 클러스터 노드)에 원격으로 연결하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: aljo
ms.openlocfilehash: 3c7b3626db0e38d28513d4665a83dd7155663034
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>가상 머신 확장 집합 인스턴스 또는 클러스터 노드에 원격 연결
Azure에서 실행되는 Service Fabric 클러스터에서 정의한 각 클러스터 노드 형식은 [가상 머신별 규모를 설정](service-fabric-cluster-nodetypes.md)합니다.  특정 확장 집합 인스턴스(또는 클러스터 노드)에 원격 연결할 수 있습니다.  단일 인스턴스 VM과 달리 확장 집합 인스턴스는 고유한 가상 IP 주소가 없습니다. 따라서 특정 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 포트를 찾는 것이 어려울 수 있습니다.

특정 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 특정 인스턴스를 찾으려면 다음 단계를 완료합니다.

1. 원격 데스크톱 프로토콜(RDP)에 대한 인바운드 NAT 규칙을 가져와 노드 형식에 대한 가상 IP 주소를 찾습니다.

    먼저 `Microsoft.Network/loadBalancers`에 대해 정의된 리소스 정의의 일부로 정의된 인바운드 NAT 규칙 값을 가져옵니다.
    
    Azure Portal의 부하 분산 장치 페이지에서 **설정** > **인바운드 NAT 규칙**을 선택합니다. 이렇게 하면 첫 번째 확장 집합 인스턴스에 원격으로 연결하는 데 사용할 수 있는 IP 주소 및 포트가 나타납니다. 
    
    ![부하 분산 장치][LBBlade]
    
    다음 그림에서는 IP 주소 및 포트가 **104.42.106.156** 및 **3389**입니다.
    
    ![NAT 규칙][NATRules]

2. 특정 확장 집합 인스턴스 또는 노드에 원격으로 연결하는 데 사용할 수 있는 포트를 찾습니다.

    확장 집합 인스턴스가 노드에 매핑됩니다. 확장 집합 정보를 사용하여 사용할 정확한 포트를 판단합니다.
    
    포트는 확장 집합 인스턴스에 맞게 오름차순으로 할당됩니다. 앞의 FrontEnd 노드 형식 예에서 다음 표는 5개 노드 인스턴스 각각에 대한 포트를 열거합니다. 확장 집합 인스턴스에 동일한 매핑을 적용합니다.
    
    | **가상 머신 확장 집합 인스턴스** | **포트** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. 특정 확장 집합 인스턴스에 원격으로 연결합니다.

    다음 그림에서는 원격 데스크톱 연결을 사용하여 FrontEnd_1 확장 집합 인스턴스에 연결하는 것을 보여 줍니다. 
    
    ![원격 데스크톱 연결][RDP]


다음 단계는 아래 문서를 참조하세요.
* ["어디에나 배포" 기능의 개요 및 Azure 관리된 클러스터와 비교](service-fabric-deploy-anywhere.md)를 참조하세요.
* [클러스터 보안](service-fabric-cluster-security.md)에 대해 알아보기
* 배포 후에 클러스터 VM에서 [RDP 포트 범위 값 업데이트](./scripts/service-fabric-powershell-change-rdp-port-range.md)
* 클러스터 VM에 대한 [관리자 사용자 이름 및 암호 변경](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md)

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
