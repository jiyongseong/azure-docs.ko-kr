---
title: Azure 리소스 공급자 등록 오류 | Microsoft Docs
description: Azure 리소스 공급자 등록 오류를 해결하는 방법을 설명합니다.
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
ms.openlocfilehash: b90009c1cd08a1004e58c4b9f25cd6350712fbcd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="resolve-errors-for-resource-provider-registration"></a>리소스 공급자 등록 오류 해결

이 문서에서는 이전에 구독에 사용하지 않은 리소스 공급자를 사용할 때 발생할 수 있는 오류에 대해 설명합니다.

## <a name="symptom"></a>증상

리소스를 배포할 때 다음 오류 코드 및 메시지가 나타날 수 있습니다.

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

또는 다음과 유사한 메시지가 나타날 수 있습니다.

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

오류 메시지는 지원되는 위치 및 API 버전에 대해 제안을 제공해야 합니다. 템플릿을 제안된 값 중 하나로 변경할 수 있습니다. 대부분의 공급자는 Azure 포털이나 사용 중인 명령줄 인터페이스에 의해 자동으로 등록되지만, 그렇지 않은 경우도 있습니다. 특정 리소스 공급자를 전에 사용하지 않은 경우 해당 공급자를 등록해야 합니다.

## <a name="cause"></a>원인

세 가지 이유 중 하나로 이러한 오류가 나타납니다.

1. 리소스 공급자가 구독에 대해 등록되지 않았습니다.
1. 해당 리소스 종류에 대해 API 버전이 지원되지 않습니다.
1. 해당 리소스 종류에 대해 위치가 지원되지 않습니다.

## <a name="solution-1---powershell"></a>해결 방법 1 - PowerShell

PowerShell의 경우 **Get-AzureRmResourceProvider**를 사용하여 등록 상태를 확인합니다.

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

공급자를 등록하려면 **Register-AzureRmResourceProvider** 를 사용하여 등록할 리소스 공급자의 이름을 제공합니다.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

특정 종류의 리소스에 대해 지원되는 위치를 확인하려면 다음을 사용합니다.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

특정 종류의 리소스에 대해 지원되는 API 버전을 확인하려면 다음을 사용합니다.

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>해결 방법 2 - Azure CLI

공급자가 등록되어 있는지 확인하려면 `az provider list` 명령을 사용합니다.

```azurecli-interactive
az provider list
```

리소스 공급자를 등록하려면 `az provider register` 명령을 사용하고 등록할 *네임스페이스* 를 지정합니다.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

리소스 유형에 대해 지원되는 위치 및 API 버전을 보려면 다음을 사용합니다.

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>해결 방법 3 - Azure Portal

등록 상태를 볼 수 있으며 포털을 통해 리소스 공급자 네임스페이스를 등록할 수 있습니다.

1. 구독의 경우 **리소스 공급자**를 선택합니다.

   ![리소스 공급자 선택](./media/resource-manager-register-provider-errors/select-resource-provider.png)

1. 리소스 공급자의 목록을 확인하고 필요한 경우 **등록** 링크를 선택하여 배포하려는 유형의 리소스 공급자를 등록합니다.

   ![리소스 공급자 나열](./media/resource-manager-register-provider-errors/list-resource-providers.png)
