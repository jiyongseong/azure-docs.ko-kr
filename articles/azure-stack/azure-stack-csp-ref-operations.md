---
title: Azure 스택에서 추적 사용에 대 한 테 넌 트가 등록 | Microsoft Docs
description: 테 넌 트 등록 및 Azure 스택의 테 넌 트 사용을 추적 하는 방식을 관리 하는 데 사용 되는 작업에 대 한 세부 정보입니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: ef7ca59647a1f8c15d85c809609060a5945bedde
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Azure 스택의 테 넌 트 등록 관리

*적용 대상: Azure 스택 시스템 통합*

이 문서는 테 넌 트 등록을 및 테 넌 트 사용을 추적 하는 방식을 관리 하는 데 사용할 수는 작업에 대 한 세부 정보를 포함 합니다. 추가, 목록 또는 테 넌 트 매핑을 제거 하는 방법에 대 한 세부 정보를 찾을 수 있습니다. 추적 사용을 관리 하려면 PowerShell 또는 청구 API 끝점을 사용할 수 있습니다.

## <a name="add-tenant-to-registration"></a>등록을 위해 테 넌 트 추가

용도 자신의 Azure Active Directory (Azure AD) 테 넌 트와 연결 된 Azure 구독에서 보고 되도록 프로그램 등록을 위해 새 테 넌 트를 추가 하려는 경우이 작업을 사용 합니다.

테 넌 트와 연결 된 구독을 변경 하려는 경우에이 작업을 사용할 수 있습니다, PUT/새로 만들기-AzureRMResource를 다시 호출할 수 있습니다. 오래 된 매핑을 덮어씁니다.

Azure 구독이 하나만 테 넌 트와 연결할 수 있는지 확인 합니다. 기존 테 넌 트에 두 번째 구독을 추가 하려고 하면 첫 번째 구독 덮어쓰게 됩니다. 


| 매개 변수                  | 설명 |
|---                         | --- |
| registrationSubscriptionID | 초기 등록에 사용 된 Azure 구독입니다. |
| customerSubscriptionID     | Azure 구독 (Azure 스택 되지 않음) 등록할 수 있도록 고객에 속한입니다. 만들어야 하며, 클라우드 서비스 공급자 (CSP) 제공에서 합니다. 실제로이 파트너 센터를 통해 의미합니다. 고객에 게 둘 이상의 테 넌 트를이 구독의 Azure 스택에 로그인 하는 데 사용할 테 넌 트에 만들어야 합니다. |
| resourceGroup              | 등록 저장 되어 있는 Azure의 리소스 그룹입니다. |
| registrationName           | Azure 스택 등록의 이름입니다. 이 Azure에 저장 된 개체입니다. 양식 azurestack-CloudID, 여기서 CloudID는 Azure 스택 배포의 클라우드 ID에에서는 이름이 일반적으로 나타납니다. |

> [!Note]  
> 테 넌 트를 사용 하는 각 Azure 스택에 등록 해야 합니다. 테 넌 트 둘 이상의 Azure 스택을 사용 하면 테 넌 트 구독에 각 배포의 초기 등록을 업데이트 해야 합니다.

### <a name="powershell"></a>PowerShell

등록 리소스를 업데이트 하는 새로운 AzureRmResource cmdlet을 사용 합니다. Azure에 로그인 (`Add-AzureRmAccount`) 초기 등록에 사용한 계정을 사용 하 여 합니다. 테 넌 트를 추가 하는 방법의 예는 다음과 같습니다.

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API 호출

**작업**: 배치  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**응답**: 201 생성 됨  
**응답 본문**: 빈  

## <a name="list-all-registered-tenants"></a>등록 된 모든 테 넌 트를 나열 합니다.

에 등록 하는 추가 된 모든 테 넌 트의 목록을 가져옵니다.

 > [!Note]  
 > 테 넌 트가 없는 하도록 등록 된 경우에 응답을 받을 수 없습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수                  | 설명          |
|---                         | ---                  |
| registrationSubscriptionId | 초기 등록에 사용 된 Azure 구독입니다.   |
| resourceGroup              | 등록 저장 되어 있는 Azure의 리소스 그룹입니다.    |
| registrationName           | Azure 스택 등록의 이름입니다. 이 Azure에 저장 된 개체입니다. 이름은 일반적으로의 형태로 **azurestack**-***CloudID***여기서 ***CloudID*** Azure 스택 배포의 클라우드 ID입니다.   |

### <a name="powershell"></a>PowerShell

등록 된 모든 테 넌 트를 나열 하려면 AzureRmResovurce Get cmdlet을 사용 합니다. Azure에 로그인 (`Add-AzureRmAccount`) 초기 등록에 사용한 계정을 사용 하 여 합니다. 테 넌 트를 추가 하는 방법의 예는 다음과 같습니다.

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 호출

가져오기 작업을 사용 하 여 모든 테 넌 트 매핑 목록을 가져올 수 있습니다.

**작업**: 가져오기  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**응답**: 200  
**응답 본문**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>테 넌 트 매핑이 제거

에 등록 하는 추가 된 테 넌 트를 제거할 수 있습니다. 해당 테 넌 트 Azure 스택에 리소스를 계속 사용은, 용도 초기 Azure 스택 등록에 사용 되는 구독에 청구 됩니다.

### <a name="parameters"></a>매개 변수

| 매개 변수                  | 설명          |
|---                         | ---                  |
| registrationSubscriptionId | 등록에 대 한 구독 ID입니다.   |
| resourceGroup              | 등록에 대 한 리소스 그룹입니다.   |
| registrationName           | 등록의 이름입니다.  |
| customerSubscriptionId     | 고객 구독 id입니다.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 호출

삭제 작업을 사용 하 여 테 넌 트 매핑을 제거할 수 있습니다.

**작업**: 삭제  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**응답**: 204 콘텐츠 없음  
**응답 본문**: 빈

## <a name="next-steps"></a>다음 단계

 - 스택에서 Azure 리소스 사용 정보를 검색 하는 방법에 대 한 자세한 참조 [사용 및 청구 Azure 스택의](/azure-stack-billing-and-chargeback.md)합니다.
