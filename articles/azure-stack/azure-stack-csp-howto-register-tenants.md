---
title: 사용에 대 한 테 넌 트를 추가 하 고 Azure 스택에 청구 | Microsoft Docs
description: 필요한 단계는 클라우드 서비스 공급자가 관리 하는 Azure 스택에 최종 사용자를 추가 합니다.
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
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: e982fa2bec3cbc4845ecebb45db76f019e2178ff
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>사용에 대 한 테 넌 트를 추가 하 고 Azure 스택에 청구

*적용 대상: Azure 스택 시스템 통합*

이 문서에서는 필요한 단계를 설명 최종 사용자는 클라우드 서비스 공급자 (CSP)에서 관리 되는 Azure 스택에 추가 합니다. 새 테 넌 트 리소스를 사용할 때 Azure 스택 CSP 구독에 사용량을 보고 합니다.

Csp는 종종 Azure 스택 배포에서 여러 고객 (테 넌 트)에 서비스를 제공 합니다. 각 테 넌 트 사용 현황 보고 되며 해당 하는 CSP 구독 요금이 청구 되도록 Azure 스택 등록에 테 넌 트를 추가 합니다. 이 문서의 단계를 완료 하면 테 넌 트 사용 현황 Azure 스택의 초기 등록에 사용 되는 구독에 요금이 부과 됩니다. 테 넌 트를 관리 하 고 추적 사용에 대 한 Azure 스택에 최종 사용자를 추가 하려면, 먼저 Azure 스택 CSP를 구성 해야 합니다. 단계 및 리소스에 대 한 참조 [관리 사용 현황 및 클라우드 서비스 공급자로 Azure 스택에 대 한 요금 청구](azure-stack-add-manage-billing-as-a-csp.md)합니다.

다음 다이어그램은 CSP Azure 스택을 사용 하 고 사용 고객에 대 한 추적을 설정 하려면 새 고객을 사용 하도록 설정 하기 위해 수행 해야 하는 단계를 보여 줍니다. 최종 사용자를 추가 하 여 스택에서 Azure 리소스를 관리할 수도 있습니다. 해당 리소스를 관리 하기 위한 두 가지 옵션이 있습니다.

1. 최종 고객 테 넌 트를 유지 관리할 수 있으며 최종 고객에 게 로컬 스택 Azure 구독에 자격 증명을 제공 수 있습니다.  
2. 또는 최종 사용자의 구독을 로컬로 사용 하 고 CSP 게스트로 소유자 권한을 추가 합니다.  

**최종 사용자를 추가 하는 단계**

![최종 고객 계정을 관리 하 고 추적 사용에 대 한 클라우드 서비스 공급자를 설정](media\azure-stack-csp-enable-billing-usage-tracking\process-csp-enable-billing.png)

## <a name="create-a-new-customer-in-partner-center"></a>파트너 센터에 새 고객을 만들으십시오

파트너 센터에서 고객에 대 한 새 Azure 구독을 만듭니다. 자세한 내용은 [새 고객을 추가](https://msdn.microsoft.com/partner-center/add-a-new-customer)합니다.


##  <a name="create-an-azure-subscription-for-the-end-customer"></a>최종 사용자에 대 한 Azure 구독 만들기

파트너 센터에서 고객의 레코드를 만든 후 판매할 수 있습니다 이러한 카탈로그의 제품에 대 한 구독. 자세한 내용은 [만들기, 일시 중단 또는 고객 구독을 취소](https://msdn.microsoft.com/partner-center/create-a-new-subscription)합니다.

## <a name="create-a-guest-user-in-the-end-customer-directory"></a>최종 고객 디렉터리에서 게스트 사용자를 만듭니다.

최종 사용자가 자신의 계정을 관리 하는 경우 해당 디렉터리에 게스트 사용자를 만들고 정보를 보낼 합니다. 최종 사용자 다음 게스트를 추가 하 고 게스트 권한을 상승 **소유자** 스택 CSP Azure 계정에 있습니다.
 
## <a name="update-the-registration-with-the-end-customer-subscription"></a>최종 고객 구독을 사용 하 여 등록 업데이트

새 고객의 구독을 등록을 업데이트 합니다. Azure는 파트너 중앙에서 고객 id를 사용 하 여 고객의 사용을 보고 합니다. 이렇게 하면 각 고객의 사용은 해당 고객의 개별 CSP 구독에서 보고 됩니다. 그러면 사용자 사용 현황 추적 및 요금 청구 훨씬 더 쉽습니다.

> [!Note]  
> 이 단계를 수행 하려면 있어야 [Azure 스택 등록](azure-stack-register.md)합니다.

1. 관리자 권한 프롬프트를 통한 Windows PowerShell을 열고 실행 합니다.  
    `Add-AzureRmAccount`
2. Azure 자격 증명을 입력 합니다.
3. PowerShell 세션에서 다음을 실행 합니다.

```powershell
    New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```
### <a name="new-azurermresource-powershell-parameters"></a>새 AzureRmResource PowerShell 매개 변수
| 매개 변수 | 설명 |
| --- | --- | 
|registrationSubscriptionID | Azure 스택의 초기 등록에 사용 된 Azure 구독입니다. |
| customerSubscriptionID | Azure 구독 (Azure 스택 되지 않음) 등록할 수 있도록 고객에 속한입니다. 해야 CSP 제안;에 만든 실제로이 파트너 센터를 통해 의미합니다. 고객에 Azure Active Directory 테 넌 트를 여러 개 있는 경우이 구독은 Azure 스택에 로그인 하는 데 사용할 테 넌 트에 만들어야 합니다.
| resourceGroup | 등록 저장 되어 있는 Azure의 리소스 그룹입니다. 
| registrationName | Azure 스택 등록의 이름입니다. 이 Azure에 저장 된 개체입니다. | 


> [!Note]  
> 테 넌 트를 사용 하는 각 Azure 스택에 등록 해야 합니다. 두 개의 Azure 스택 배포 있고 테 넌 트 둘 다 사용 하려는 경우 테 넌 트 구독에 각 배포의 초기 등록을 업데이트 해야 합니다.

## <a name="onboard-tenant-to-azure-stack"></a>온보드 테 넌 트 Azure 스택

Azure 스택 스택에서 Azure 서비스를 사용 하도록 여러 Azure AD 테 넌 트의 사용자를 지원 하도록 구성 합니다. 자세한 내용은 [Azure 스택에서 다중 테 넌 트를 사용 하도록 설정](azure-stack-enable-multitenancy.md)합니다.


## <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>최종 고객 테 넌 트 Azure 스택의에서 로컬 리소스 만들기

Azure 스택에 새 고객을 추가한 하거나 최종 고객 테 넌 트 소유자 권한 사용 하 여 게스트 계정을 사용 하도록 설정한 경우 테 넌 트에 리소스를 만들 수 있는지 확인 합니다. 예를 들어, 수행할 수 있습니다 [스택 Azure 포털과 Windows 가상 컴퓨터를 만들](user\azure-stack-quick-windows-portal.md)합니다.

## <a name="next-steps"></a>다음 단계

 - 등록 과정에서 트리거되는 경우 오류 메시지를 검토 하려면 참조 [등록 오류 메시지를 테 넌 트](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes)합니다.
 - 스택에서 Azure 리소스 사용 정보를 검색 하는 방법에 대 한 자세한 참조 [사용 및 청구 Azure 스택의](/azure-stack-billing-and-chargeback.md)합니다.
 - 방법을 최종 사용자 수로 추가할 CSP를 검토 하려면 테 넌 트 관리자가 Azure 스택에 대 한 자세한 내용은 [스택 Azure 구독을 관리 하는 클라우드 서비스 공급자를 사용 하도록 설정](user\azure-stack-csp-enable-billing-usage-tracking.md)합니다.
