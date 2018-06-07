---
title: Azure에 대한 vCPU 할당량 | Microsoft Docs
description: Azure에 대한 vCPU 할당량에 대해 알아봅니다.
keywords: ''
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: cynthn
ms.openlocfilehash: 50f28fcb2d7945949134d89f6bcc4238cf625bd1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="virtual-machine-vcpu-quotas"></a>가상 머신 vCPU 할당량

가상 머신 및 가상 머신 확장 집합에 대한 vCPU 할당량은 각 지역의 각 구독에 대해 두 가지 계층으로 정렬됩니다. 첫 번째 계층은 지역별 총 vCPU이며 두 번째 계층은 표준 D 제품군 vCPU와 같은 다양한 VM 크기 제품군 코어입니다. 새 VM이 배포될 때마다 새롭게 배포된 VM에 대한 vCPU는 특정 VM 크기 제품군 또는 지역별 총 vCPU 할당량에 대한 vCPU 할당량을 초과하지 않아야 합니다. 이러한 할당량 중 하나가 초과되면 VM 배포가 허용되지 않습니다. 지역에서 전체 가상 머신의 수에 대한 할당량도 있습니다. 이러한 각 할당량의 세부 정보는 [Azure Portal](https://portal.azure.com)에서 **구독** 페이지의 **사용량 + 할당량** 섹션에서 볼 수 있거나 PowerShell을 사용하여 값에 대해 쿼리할 수 있습니다.

 
## <a name="check-usage"></a>사용량 확인

[Get-AzureRmVMUsage](/powershell/module/azurerm.compute/get-azurermvmusage) cmdlet을 사용하여 할당량 사용량을 확인할 수 있습니다.

```azurepowershell-interactive
Get-AzureRmVMUsage -Location "East US"
```

출력은 다음과 유사합니다.

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count

```


## <a name="reserved-vm-instances"></a>예약 VM 인스턴스
단일 구독으로 범위가 설정되는 예약 VM 인스턴스는 vCPU 할당량에 새 요소를 추가합니다. 이러한 값은 구독에 배포할 수 있어야 하는 언급된 크기의 인스턴스 수를 설명합니다. 해당 할당량이 예약 인스턴스를 구독에 배포할 수 있도록 예약되도록 할당량 시스템에서 자리 표시자로 작동합니다. 예를 들어 특정 구독에 10개의 Standard_D1 예약 인스턴스가 있는 경우 Standard_D1 예약 인스턴스에 대한 사용량 한도는 10이 됩니다. 이렇게 하면 Azure에서 Standard_D1 인스턴스에 사용될 지역별 총 vCPU 할당량에서 항상 최소 10개의 vCPU를 사용할 수 있고 Standard_D1 인스턴스에 사용될 표준 D 제품군 vCPU 할당량에서 최소 10개의 vCPU를 사용할 수 있도록 보장합니다.

단일 구독 RI를 구매하는 데 할당량 증가가 필요한 경우 구독에서 [할당량 증가를 요청](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

청구 및 할당량에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=/azure/billing/TOC.json)을 참조하세요.
