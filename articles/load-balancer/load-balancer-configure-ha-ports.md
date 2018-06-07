---
title: Azure Load Balancer에 대한 고가용성 포트 구성 | Microsoft Docs
description: 모든 포트에서 내부 트래픽의 부하를 분산하는 고가용성 포트를 사용하는 방법에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/20178
ms.author: kumud
ms.openlocfilehash: eb305986982432d7a432204e3fae8a1dff6a5d74
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>내부 부하 분산 장치에 대해 고가용성 포트 구성

이 문서에서는 내부 부하 분산 장치에 고가용성 포트를 배포하는 예제를 제공합니다. NVA(네트워크 가상 어플라이언스) 특정 구성에 대한 자세한 정보는 해당 공급자 웹 사이트를 참조하세요.

>[!NOTE]
>Azure Load Balancer는 기본 및 표준이라는 두 가지 형식을 지원합니다. 이 문서에서는 표준 Load Balancer에 대해 설명합니다. 기본 Load Balancer에 대한 자세한 내용은 [Load Balancer 개요](load-balancer-overview.md)를 참조하세요.

아래 그림은 이 문서에서 설명하는 배포 예제에 대해 다음과 같은 구성을 보여 줍니다.

- NVA는 고가용성 포트 구성 뒤에 있는 내부 부하 분산 장치의 백 엔드 풀에 배포됩니다. 
- DMZ 서브넷에 적용된 UDR(사용자 정의 경로)은 다음 홉을 내부 부하 분산 장치 가상 IP로 만들어 NVA에 모든 트래픽을 라우트합니다. 
- 내부 부하 분산 장치는 부하 분산 장치 알고리즘에 따라 활성 NVA 중 하나로 트래픽을 분산합니다.
- NVA는 트래픽을 처리하여 백 엔드 서브넷의 원래 대상으로 전달합니다.
- 해당 UDR이 백 엔드 서브넷에 구성되어 있는 경우 반환 경로로 동일한 경로를 사용할 수 있습니다. 

![고가용성 포트 예제 배포](./media/load-balancer-configure-ha-ports/haports.png)



## <a name="configure-high-availability-ports"></a>고가용성 포트 구성

고가용성 포트를 구성하려면 백 엔드 풀의 NVA를 사용하여 내부 부하 분산 장치를 설정합니다. 고가용성 포트를 사용하여 NVA 상태 및 부하 분산 장치 규칙을 검색하려면 해당 부하 분산 장치 상태 프로브 구성을 설정합니다. 일반적인 부하 분산 장치 관련 구성에 대해서는 [시작](load-balancer-get-started-ilb-arm-portal.md)을 참조하세요. 이 문서에서는 고가용성 포트 구성을 중점적으로 설명합니다.

구성에서는 기본적으로 프런트 엔드 포트와 백 엔드 포트 값을 **0**으로 설정합니다. 프로토콜 값은 **All**로 설정합니다. 이 문서에서는 Azure Portal, PowerShell 및 Azure CLI 2.0을 사용하여 고가용성 포트를 구성하는 방법에 대해 설명합니다.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Azure Portal을 사용하여 고가용성 포트 부하 분산 장치 규칙 구성

Azure Portal을 사용하여 고가용성 포트를 구성하려면 **HA 포트** 확인란을 선택합니다. 이 옵션을 선택하면 관련 포트 및 프로토콜 구성이 자동으로 채워집니다. 

![Azure Portal을 통해 고가용성 포트 구성](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Resource Manager 템플릿을 통해 고가용성 포트 부하 분산 규칙 구성

Load Balancer 리소스에서 Microsoft.Network/loadBalancers의 2017-08-01 API 버전을 사용하여 고가용성 포트를 구성할 수 있습니다. 다음 JSON 코드 조각은 REST API를 통한 고가용성 포트 부하 분산 장치 구성의 변경 내용을 보여 줍니다.

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>PowerShell을 사용하여 고가용성 포트 부하 분산 장치 규칙 구성

PowerShell을 사용하여 내부 부하 분산 장치를 만드는 동안 다음 명령을 사용하여 고가용성 포트 부하 분산 장치 규칙을 만듭니다.

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>Azure CLI 2.0을 사용하여 고가용성 포트 부하 분산 장치 규칙 구성

[내부 부하 분산 장치 집합 만들기](load-balancer-get-started-ilb-arm-cli.md) 4단계에서 다음 명령을 사용하여 고가용성 포트 부하 분산 장치 규칙을 만듭니다.

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>다음 단계

[고가용성 포트](load-balancer-ha-ports-overview.md)에 대해 자세히 알아봅니다.
