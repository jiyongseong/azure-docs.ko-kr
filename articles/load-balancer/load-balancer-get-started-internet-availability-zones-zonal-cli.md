---
title: Azure CLI를 사용하여 영역별 프런트 엔드가 있는 Load Balancer Standard 만들기 | Microsoft Docs
description: Azure CLI를 사용하여 영역별 공용 IP 주소 프런트 엔드가 있는 Load Balancer Standard를 만드는 방법 알아보기
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2018
ms.author: kumud
ms.openlocfilehash: da18693f090a256bf69ea27e46e0ac010eb293b0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zonal-frontend-using-azure-cli"></a>Azure CLI를 사용하여 영역별 공용 IP 주소 프런트 엔드가 있는 Load Balancer Standard 만들기

이 문서에서는 영역별 프런트 엔드가 있는 공용 [Load Balancer Standard](https://aka.ms/azureloadbalancerstandard)를 만드는 단계를 안내합니다. 영역별 프런트 엔드란 모든 인바운드 또는 아웃바운드 흐름이 한 지역의 단일 영역에서 처리된다는 뜻입니다. 프런트 엔드 구성에서 영역별 표준 공용 IP 주소를 사용하여 영역별 프런트 엔드가 있는 부하 분산 장치를 만들 수 있습니다. Standard Load Balancer에서 가용성 영역이 작동하는 방법에 대한 내용은 [Standard Load Balancer 및 가용성 영역](load-balancer-standard-availability-zones.md)을 참조하세요. 

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 최신 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)을 설치했고 [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az_login)을 사용하여 Azure 계정에 로그인했는지 확인합니다.

> [!NOTE]
> Azure 리소스, 지역 및 VM 크기 제품군을 선택하는 데 가용성 영역에 대한 지원을 사용할 수 있습니다. 시작하는 방법에 대한 자세한 내용 및 가용성 영역을 사용해 볼 수 있는 Azure 리소스, 지역 및 VM 크기 제품군은 [가용성 영역 개요](https://docs.microsoft.com/azure/availability-zones/az-overview)를 참조하세요. 지원을 위해 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones)에 연결하거나 [Azure 지원 티켓을 열](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 수 있습니다. 


## <a name="create-a-resource-group"></a>리소스 그룹 만들기

다음 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroupZLB --location westeurope
```

## <a name="create-a-public-standard-ip-address"></a>공용 표준 IP 주소 만들기

다음 명령을 사용하여 표준 공용 IP 주소를 만듭니다.

```azurecli-interactive
az network public-ip create --resource-group myResourceGroupZLB --name myPublicIPZonal --sku Standard --zone 1
```

## <a name="create-a-load-balancer"></a>부하 분산 장치 만들기

다음 명령을 통해 앞 단계에서 만든 표준 공용 IP를 사용하여 공용 부하 분산 장치 표준을 만듭니다.

```azurecli-interactive
az network lb create --resource-group myResourceGroupZLB --name myLoadBalancer --public-ip-address myPublicIPZonal --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool --sku Standard
```

## <a name="create-an-lb-probe-on-port-80"></a>포트 80에서 LB 프로브 만들기

다음 명령을 사용하여 부하 분산 장치 상태 프로브를 만듭니다.

```azurecli-interactive
az network lb probe create --resource-group myResourceGroupZLB --lb-name myLoadBalancer \
  --name myHealthProbe --protocol tcp --port 80
```

## <a name="create-an-lb-rule-for-port-80"></a>포트 80에 대한 LB 규칙 만들기

다음 명령을 사용하여 부하 분산 장치 규칙을 만듭니다.

```azurecli-interactive
az network lb rule create --resource-group myResourceGroupZLB --lb-name myLoadBalancer --name myLoadBalancerRuleWeb \
  --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEnd \
  --backend-pool-name myBackEndPool --probe-name myHealthProbe
```

## <a name="next-steps"></a>다음 단계
- [Standard Load Balancer 및 가용성 영역](load-balancer-standard-availability-zones.md)에 대해 자세히 알아봅니다.



