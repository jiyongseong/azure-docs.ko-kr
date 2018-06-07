---
title: Azure 할당량 오류 | Microsoft Docs
description: 리소스 할당량 오류의 해결 방법을 설명합니다.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 6d9048ae531abedb89b70989ce1c962357c514cd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-errors-for-resource-quotas"></a>리소스 할당량 오류 해결

이 문서에서는 리소스를 배포할 때 발생할 수 있는 할당량 오류에 대해 설명합니다.

## <a name="symptom"></a>증상

Azure 할당량을 초과하는 리소스를 만드는 템플릿을 배포할 때 다음과 같은 배포 오류 메시지가 표시됩니다.

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

또는 다음이 표시될 수 있습니다.

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>원인

할당량은 리소스 그룹, 구독, 계정 및 기타 범위에 따라 적용됩니다. 예를 들어 지역에 대한 코어 수를 제한하도록 구독을 구성할 수 있습니다. 허용량보다 많은 코어가 있는 가상 머신을 배포하려는 경우 할당량을 초과했다는 오류 메시지가 표시됩니다.
전체 할당량 정보는 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="azure-cli"></a>Azure CLI

Azure CLI의 경우 `az vm list-usage` 명령을 사용하여 가상 머신 할당량을 찾습니다.

```azurecli
az vm list-usage --location "South Central US"
```

반환하는 내용은 다음과 같습니다.

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

PowerShell의 경우 **Get AzureRmVMUsage** 명령을 사용하여 가상 머신 할당량을 찾습니다.

```powershell
Get-AzureRmVMUsage -Location "South Central US"
```

반환하는 내용은 다음과 같습니다.

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>해결 방법

할당량 증가를 요청하려면 포털로 이동한 후 지원 문제를 제출합니다. 지원 문제에서는 배포하려는 지역에 대한 할당량 증가를 요청합니다.

> [!NOTE]
> 리소스 그룹에 대한 할당량은 구독 전체가 아니라 각 개별 지역에 대한 할당량입니다. 사용자가 미국 서부에 30개의 코어를 배포해야 하면 미국 서부에 30개의 리소스 관리자 코어를 요청해야 합니다. 사용자가 액세스하는 임의의 지역에서 30개 코어를 배포해야 하는 경우 모든 지역에 대해 30개 Resource Manager 코어를 요청해야 합니다.
>
>

1. **구독**을 선택합니다.

   ![구독](./media/resource-manager-quota-errors/subscriptions.png)

2. 할당량을 늘려야 하는 구독을 선택합니다.

   ![구독 선택](./media/resource-manager-quota-errors/select-subscription.png)

3. **사용량 + 할당량**을 선택합니다.

   ![사용량 + 할당량 선택](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. 오른쪽 위 모서리에서 **증가 요청**을 선택합니다.

   ![증가 요청](./media/resource-manager-quota-errors/request-increase.png)

5. 늘려야 할 할당량 형식에 대한 양식을 작성합니다.

   ![양식 작성](./media/resource-manager-quota-errors/forms.png)