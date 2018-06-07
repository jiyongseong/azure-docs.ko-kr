---
title: Azure 리소스 정책의 RequestDisallowedByPolicy 오류 | Microsoft Docs
description: RequestDisallowedByPolicy 오류의 원인을 설명합니다.
services: azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: genli
ms.openlocfilehash: 474400d92660b68fd7fef906216b8e37c6e8c94d
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Azure 리소스 정책의 RequestDisallowedByPolicy 오류

이 문서에서는 RequestDisallowedByPolicy 오류의 원인을 설명하고 해결 방법을 제공합니다.

## <a name="symptom"></a>증상

배포하는 동안 리소스를 만들지 못하게 방해하는 **RequestDisallowedByPolicy** 오류가 발생할 수 있습니다. 다음 예제에서는 오류가 발생합니다.

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>문제 해결

배포를 차단한 정책에 대한 세부 정보를 검색하려면 다음 방법 중 하나를 사용합니다.

### <a name="powershell"></a>PowerShell

PowerShell에서 해당 정책 식별자를 `Id` 매개 변수로 제공하여 배포를 차단한 정책에 대한 정보를 검색합니다.

```PowerShell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI 2.0에서 정책 정의 이름을 제공합니다.

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>해결 방법

보안 또는 규정 준수를 위해 구독 관리자는 리소스가 배포되는 방식을 제한하는 정책을 할당할 수 있습니다. 예를 들어, 구독에는 공용 IP 주소, 네트워크 보안 그룹, 사용자 정의 경로 또는 경로 테이블을 만들지 못하게 하는 정책이 있을 수 있습니다. **증상** 섹션의 오류 메시지는 정책의 이름을 표시합니다.
이 문제를 해결하려면 리소스 정책을 검토하고 해당 정책을 준수하는 리소스를 배포하는 방법을 결정합니다.

자세한 내용은 다음 문서를 참조하세요.

- [Azure Policy이란?](../azure-policy/azure-policy-introduction.md)
- [규정 준수를 적용하는 정책 만들기 및 관리](../azure-policy/create-manage-policy.md)
