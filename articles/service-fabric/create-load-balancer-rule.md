---
title: 클러스터에 대한 Azure Load Balancer 규칙 만들기
description: Azure Service Fabric 클러스터에 대한 포트를 열도록 Azure Load Balancer를 구성합니다.
services: service-fabric
documentationcenter: na
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: adegeo
ms.openlocfilehash: 53dcd6c0705faa94e83d6e44f813fa9c575843e8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="open-ports-for-a-service-fabric-cluster"></a>Service Fabric 클러스터에 대한 포트 열기

Azure Service Fabric 클러스터를 사용하여 배포한 부하 분산 장치는 트래픽을 노드에서 실행되는 앱으로 보냅니다. 다른 포트를 사용하도록 앱을 변경하면 Azure Load Balancer에서 해당 포트를 노출(또는 다른 포트로 라우팅)해야 합니다.

Azure에 Service Fabric 클러스터를 배포한 경우 부하 분산 장치가 자동으로 만들어집니다. 부하 분산 장치가 없는 경우 [인터넷 연결 부하 분산 장치 구성](..\load-balancer\load-balancer-get-started-internet-portal.md)을 참조하세요.

## <a name="configure-service-fabric"></a>Service Fabric 구성

Service Fabric 응용 프로그램 **ServiceManifest.xml** 구성 파일은 응용 프로그램에 사용하는 끝점을 정의합니다. 구성 파일이 끝점을 정의하도록 업데이트된 후에 부하 분산 장치를 업데이트하여 해당(또는 다른) 포트를 노출해야 합니다. Service Fabric 끝점을 만드는 방법에 대한 자세한 내용은 [끝점 설정](service-fabric-service-manifest-resources.md)을 참조하세요.

## <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

Load Balancer 규칙은 인터넷 연결 포트를 열고 응용 프로그램에서 사용하는 내부 노드의 포트에 트래픽을 전달합니다. 부하 분산 장치가 없는 경우 [인터넷 연결 부하 분산 장치 구성](..\load-balancer\load-balancer-get-started-internet-portal.md)을 참조하세요.

Load Balancer 규칙을 만들려면 다음 정보를 수집해야 합니다.

- 부하 분산 장치 이름
- 부하 분산 장치 및 Service Fabric 클러스터의 리소스 그룹
- 외부 포트
- 내부 포트

## <a name="azure-cli"></a>Azure CLI
**Azure CLI**에서 부하 분산 장치 규칙을 만들려면 명령 1개만 사용하면 됩니다. 새 규칙을 만들려는 경우 부하 분산 장치 이름 및 리소스 그룹을 알고 있으면 됩니다.

>[!NOTE]
>부하 분산 장치의 이름을 확인해야 할 경우 다음 명령을 사용하여 모든 부하 분산 장치 및 연결된 리소스 그룹의 목록을 빠르게 표시합니다.
>
>`az network lb list --query "[].{ResourceGroup: resourceGroup, Name: name}"`
>


```azurecli
az network lb rule create --backend-port 40000 --frontend-port 39999 --protocol Tcp --lb-name LB-svcfab3 -g svcfab_cli -n my-app-rule
```

Azure CLI 명령에는 다음 표에 설명되어 있는 몇 가지 매개 변수를 사용할 수 있습니다.

| 매개 변수 | 설명 |
| --------- | ----------- |
| `--backend-port`  | Service Fabric 응용 프로그램이 수신 대기하는 포트입니다. |
| `--frontend-port` | 부하 분산 장치가 외부 연결을 위해 노출하는 포트입니다. |
| `-lb-name` | 변경할 부하 분산 장치의 이름입니다. |
| `-g`       | 부하 분산 장치 및 Service Fabric 클러스터를 모두 포함하는 리소스 그룹입니다. |
| `-n`       | 규칙의 선택한 이름입니다. |


>[!NOTE]
>Azure CLI를 사용하여 부하 분산 장치를 만드는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 부하 분산 장치 만들기](..\load-balancer\load-balancer-get-started-internet-arm-cli.md)를 참조하세요.

## <a name="powershell"></a>PowerShell

PowerShell은 Azure CLI보다 약간 더 복잡합니다. 규칙을 만드는 개념적 단계를 수행합니다.

1. Azure에서 부하 분산 장치를 가져옵니다.
2. 규칙을 만듭니다.
3. 부하 분산 장치에 규칙을 추가합니다.
4. 부하 분산 장치를 업데이트합니다.

>[!NOTE]
>부하 분산 장치의 이름을 확인해야 할 경우 다음 명령을 사용하여 모든 부하 분산 장치 및 연결된 리소스 그룹의 목록을 빠르게 표시합니다.
>
>`Get-AzureRmLoadBalancer | Select Name, ResourceGroupName`

```powershell
# Get the load balancer
$lb = Get-AzureRmLoadBalancer -Name LB-svcfab3 -ResourceGroupName svcfab_cli

# Create the rule based on information from the load balancer.
$lbrule = New-AzureRmLoadBalancerRuleConfig -Name my-app-rule7 -Protocol Tcp -FrontendPort 39990 -BackendPort 40009 `
                                            -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
                                            -BackendAddressPool  $lb.BackendAddressPools[0] `
                                            -Probe $lb.Probes[0]

# Add the rule to the load balancer
$lb.LoadBalancingRules.Add($lbrule)

# Update the load balancer on Azure
$lb | Set-AzureRmLoadBalancer
```

`New-AzureRmLoadBalancerRuleConfig` 명령과 관련해서 `-FrontendPort`는 부하 분산 장치가 외부 연결을 위해 노출하는 포트이고 `-BackendPort`는 Service Fabric 앱이 수신 대기하는 포트입니다.

>[!NOTE]
>PowerShell을 사용하여 부하 분산 장치를 만드는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 부하 분산 장치 만들기](..\load-balancer\load-balancer-get-started-internet-arm-ps.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[Service Fabric의 네트워킹](service-fabric-patterns-networking.md)에 대해 자세히 알아봅니다.