---
title: Azure에 대한 vCPU 할당량 | Microsoft Docs
description: Azure에 대한 vCPU 할당량에 대해 알아봅니다.
keywords: ''
services: virtual-machines-linux
documentationcenter: ''
author: Drewm3
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: drewm
ms.openlocfilehash: a4e0bbe1c6d9b121dfb422934cdd67ff19f80482
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="virtual-machine-vcpu-quotas"></a>가상 머신 vCPU 할당량

가상 머신 및 가상 머신 확장 집합에 대한 vCPU 할당량은 각 지역의 각 구독에 대해 두 가지 계층으로 정렬됩니다. 첫 번째 계층은 지역별 총 vCPU이며 두 번째 계층은 표준 D 제품군 vCPU와 같은 다양한 VM 크기 제품군 코어입니다. 새 VM이 배포될 때마다 새롭게 배포된 VM에 대한 vCPU는 특정 VM 크기 제품군 또는 지역별 총 vCPU 할당량에 대한 vCPU 할당량을 초과하지 않아야 합니다. 이러한 할당량 중 하나가 초과되면 VM 배포가 허용되지 않습니다. 지역에서 전체 가상 머신의 수에 대한 할당량도 있습니다. 이러한 각 할당량의 세부 정보는 [Azure Portal](https://portal.azure.com)에서 **구독** 페이지의 **사용량 + 할당량** 섹션에서 볼 수 있거나 Azure CLI를 사용하여 값에 대해 쿼리할 수 있습니다.


## <a name="check-usage"></a>사용량 확인

[az vm list-usage](/cli/azure/vm#az_vm_list_usage)를 사용하여 할당량 사용량을 확인할 수 있습니다.

```azurecli-interactive
az vm list-usage --location "East US"
[
  …
  {
    "currentValue": 4,
    "limit": 260,
    "name": {
      "localizedValue": "Total Regional vCPUs",
      "value": "cores"
    }
  },
  {
    "currentValue": 4,
    "limit": 10000,
    "name": {
      "localizedValue": "Virtual Machines",
      "value": "virtualMachines"
    }
  },
  {
    "currentValue": 1,
    "limit": 2000,
    "name": {
      "localizedValue": "Virtual Machine Scale Sets",
      "value": "virtualMachineScaleSets"
    }
  },
  {
    "currentValue": 1,
    "limit": 10,
    "name": {
      "localizedValue": "Standard B Family vCPUs",
      "value": "standardBFamily"
    }
  },
```
## <a name="reserved-vm-instances"></a>예약 VM 인스턴스
단일 구독으로 범위가 설정되는 예약 VM 인스턴스는 vCPU 할당량에 새 요소를 추가합니다. 이러한 값은 구독에 배포할 수 있어야 하는 언급된 크기의 인스턴스 수를 설명합니다. 해당 할당량이 예약 인스턴스를 구독에 배포할 수 있도록 예약되도록 할당량 시스템에서 자리 표시자로 작동합니다. 예를 들어 특정 구독에 10개의 Standard_D1 예약 인스턴스가 있는 경우 Standard_D1 예약 인스턴스에 대한 사용량 한도는 10이 됩니다. 이렇게 하면 Azure에서 Standard_D1 인스턴스에 사용될 지역별 총 vCPU 할당량에서 항상 최소 10개의 vCPU를 사용할 수 있고 Standard_D1 인스턴스에 사용될 표준 D 제품군 vCPU 할당량에서 최소 10개의 vCPU를 사용할 수 있도록 보장합니다.

단일 구독 RI를 구매하는 데 할당량 증가가 필요한 경우 구독에서 [할당량 증가를 요청](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

청구 및 할당량에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json)을 참조하세요.
