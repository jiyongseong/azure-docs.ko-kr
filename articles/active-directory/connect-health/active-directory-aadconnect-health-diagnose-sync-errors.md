---
title: Azure AD Connect Health - 중복 특성 동기화 오류 진단 | Microsoft Docs
description: 이 문서에서는 중복 특성 동기화 오류의 진단 프로세스 및 Azure Portal에서 직접 분리된 개체 시나리오의 잠재적 해결을 설명합니다.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34305660"
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>중복 특성 동기화 오류 진단 및 수정 

## <a name="overview"></a>개요
동기화 오류를 강조 표시하는 것 외에도 Azure Active Directory Connect Health는 중복 특성 동기화 오류 문제를 해결하고 Azure AD에서 분리된 개체를 수정하는 셀프 서비스 업데이트 관리 환경을 도입합니다. 진단 기능의 주요 이점:
- 중복 특성 동기화 오류 시나리오의 범위를 좁히고 특정 해결을 나타내는 진단 프로시저 제공
- 한 번 클릭으로 오류를 해결하는 Azure AD의 전용 시나리오에 대한 수정 적용
- 이 기능을 사용하기위 해 업그레이드 또는 구성이 필요하지 않습니다.
Azure AD [중복 복원력](https://aka.ms/dupattributeresdocs)에 대한 자세한 내용을 알아봅니다.

## <a name="problems"></a>문제
### <a name="common-scenario"></a>일반적인 시나리오
**QuarantinedAttributeValueMustBeUnique** 및 **AttributeValueMustBeUnique** 동기화 오류가 발생하는 경우 일반적으로 Azure AD에서 사용자 계정 이름 또는 프록시 주소 충돌을 확인합니다. 온-프레미스 쪽에서 충돌하는 원본 개체를 업데이트하여 동기화 오류를 해결할 수 있습니다. 동기화 오류는 다음 동기화 후에 해결됩니다. 예를 들어 아래 그림은 두 명의 사용자가 해당 UserPrincipalName이 충돌한다는 것을 *Joe.J@contoso.com*으로 나타냅니다. 충돌하는 개체는 Azure AD에서 격리됩니다. 

![동기화 오류 진단 일반적인 시나리오](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>분리된 개체 시나리오
때때로 기존 사용자가 원본 앵커를 손실할 수 있습니다. 온-프레미스 AD에서 원본 개체가 삭제되지만 삭제 신호를 변경하는 작업은 Azure AD로 동기화되지 않습니다. 동기화 엔진 문제 또는 도메인 마이그레이션 등의 이유로 인해 발생할 수 있습니다. 동일한 개체가 복원되거나 다시 생성되는 경우 논리적으로 기존 사용자는 원본 앵커로부터 동기화한 사용자여야 합니다. 클라우드 전용 개체인 기존 사용자의 경우 충돌하는 사용자가 Azure AD로 동기화되어 기존 개체에 동기화된 상태로 일치될 수 없습니다. 직접 원본 앵커를 다시 매핑하는 방법은 없습니다. [기존 KB](https://support.microsoft.com/help/2647098)에 대해 자세히 알아봅니다. 예를 들어 Azure AD의 기존 개체는 Joe의 라이선스를 유지합니다. Azure AD에서 중복 특성 상태에서 다른 원본 앵커를 포함한 새로 동기화된 개체가 발생했습니다. 온-프레미스 AD에서 Joe의 변경 내용은 Azure AD에서 조의 원래 사용자(기존 개체)에 적용될 수 없습니다.  

![동기화 오류 분리된 개체 진단 시나리오](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Connect Health의 진단 및 문제 해결 단계 
진단 기능은 다음 중복 특성을 포함한 사용자 개체를 지원합니다.

| 특성 이름 | 동기화 오류 형식|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique 또는 AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique 또는 AttributeValueMustBeUnique | 
| SipProxyAddress | AttributeValueMustBeUnique | 
| OnPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> 이 기능에 액세스할 수 있으려면 RBAC 설정에서 **전역 관리자** 권한 또는 **기여자**가 필요합니다. 
>

Azure Portal의 단계를 따라 동기화 오류 세부 정보 범위를 좁히고 더 구체적인 솔루션을 제공할 수 있습니다.

![동기화 진단 오류 진단 단계](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

Azure Portal에서 특정 수정 가능한 시나리오를 식별하는 몇 가지 단계를 진행할 수 있습니다.  
1.  진단 상태 열에서 상태는 오류 사례 범위를 좁히고 Azure Active Directory에서 직접 수정하는 잠재적인 문제 해결 흐름이 있는지를 표시합니다.
| 상태 | 무엇을 의미하나요? |
| ------------------ | -----------------|
| 시작 안함 | 이 진단 프로세스를 방문하지 않았습니다. 진단 결과에 따라 잠재적으로 포털에서 동기화 오류를 직접 수정할 수 있는 방법이 있습니다. |
| 수동 수정 필요 | 이 오류는 포털에서 사용할 수 있는 수정의 기준에 맞지 않습니다. (1) 충돌하는 개체 형식이 사용자가 아닌 경우, (2) 진단 단계를 진행하고 포털에서 사용할 수 있는 수정 해결이 없는 경우일 수 있습니다. 이 경우에 온-프레미스 쪽 수정이 솔루션 중 하나입니다. [온-프레미스 수정에 대해 자세히 알아보기](https://support.microsoft.com/help/2647098) | 
| 동기화 보류 중 | 수정이 적용되었습니다. 오류가 해결될 때까지 다음 동기화 주기를 기다립니다. |
>[!IMPORTANT]
> 진단 상태 열은 각 동기화 주기 후에 재설정됩니다. 
>

2.  오류 세부 정보 블레이드에서 **진단** 단추를 클릭하여 몇 가지 질문에 답변하고 동기화 오류 세부 정보를 식별해야 합니다. 질문에 대답하여 분리된 개체 시나리오의 사례를 식별할 수 있습니다. 

3.  즉, 진단의 끝에 **닫기** 단추가 없는 경우 지정된 답변에 따라 포털에서 사용할 수 있는 빠른 수정이 없습니다. 마지막 단계에 표시된 솔루션을 참조하세요. 온-프레미스의 수정이 솔루션입니다. 닫기 단추를 클릭한 후에 현재 동기화 오류 상태는 **수동 수정 필요**로 전환합니다. 상태는 현재 동기화 주기 동안 유지됩니다.

4.  분리된 개체 사례가 식별되면 포털에서 중복 특성 동기화 오류를 직접 해결할 수 있습니다. **해결 적용** 단추를 클릭하여 프로세스를 트리거합니다. 현재 동기화 오류의 상태는 **동기화 보류 중**으로 업데이트됩니다.

5.  다음 동기화 주기 후에 목록에서 오류를 제거해야 합니다.

## <a name="how-to-answer-the-diagnosis-questions"></a>진단 질문에 답변하는 방법 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>온-프레미스 Active Directory에 사용자가 있나요?

질문은 온-프레미스 Active Directory에서 기존 사용자의 원본 개체를 식별하려고 합니다.  
1.  Active Directory에 제공된 UserPrincipalName이 있는 개체가 있는지 확인합니다. 그렇지 않은 경우, 아니요를 선택합니다.
2.  그렇다면, 개체가 동기화 범위 내에 여전히 있는지 확인합니다.  
  - DN을 사용하여 Azure AD Connector 공간에서 검색합니다.
  - 개체가 **추가 보류 중** 상태인 경우 아니요를 선택합니다. Azure AD Connect는 올바른 AD 개체에 개체를 연결할 수 없습니다.
  - 개체를 찾을 수 없는 경우 예를 선택합니다.

> 예를 들어 질문은 다음 다이어그램을 사용하여 *Joe Jackson*이 온-프레미스 Active Directory에 존재하는지 식별하려고 합니다.
**일반적인 시나리오**의 경우 두 사용자 *Joe Johnson* 및 *Joe Jackson*은 온-프레미스 Active Directory에 표시됩니다. 격리된 개체는 두 명의 다른 사용자입니다.

![동기화 오류 진단 일반적인 시나리오](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> **분리된 개체 시나리오**의 경우 단일 사용자인 *Joe Johnson*만이 온-프레미스 Active Directory에서 표시됩니다.

![동기화 오류 분리된 개체 진단 시나리오](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>이러한 두 계정이 동일한 사용자에게 속하나요?
질문을 동일한 사용자에게 속했는지를 확인하기 위해 Azure AD에서 들어오는 충돌하는 사용자 및 기존 사용자 개체를 확인합니다.  
1.  충돌하는 개체는 Azure Active Directory에 새로 동기화됩니다. 다음과 개체를 비교합니다.  
  - 표시 이름
  - 사용자 계정 이름
  - 개체 ID
2.  비교하지 못한 경우 제공된 UserPrincipalNames를 사용하여 Active Directory에 개체가 있는지 확인합니다. 둘 다 찾은 경우 아니요를 선택합니다.  

> 아래 사례에서 두 개체가 동일한 사용자 *Joe Johnson*에게 속합니다.

![동기화 오류 분리된 개체 진단 시나리오](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>분리된 개체 시나리오에 대한 수정이 적용된 후에 변경된 내용
질문의 답변에 따라 Azure AD에서 사용할 수 있는 수정 내용이 있는 경우 **해결 적용** 단추를 확인할 수 있습니다. 이 경우에 예기치 않은 Azure AD 개체와 온-프레미스 개체를 동기화합니다. "원본 앵커"를 사용하여 두 개체가 매핑됩니다. 변경 내용 적용은 다음과 같은 단계를 수행합니다.
- Azure AD에서 올바른 개체로 원본 앵커를 업데이트합니다.
- 표시되는 경우 Azure AD에서 충돌하는 개체를 삭제합니다.

![수정 후에 동기화 오류 진단](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> 수정 적용 변경 내용은 분리된 개체에만 적용됩니다.
>

위의 단계 후에 사용자는 기존 개체에 연결된 원래 리소스에 액세스할 수 있습니다. 목록 보기의 **상태 진단** 값은 **동기화 보류 중**으로 업데이트됩니다. 동기화 오류는 다음 동기화 후에 해결됩니다. Connect Health는 목록 보기의 동기화 오류를 더 이상 표시하지 않습니다. 


## <a name="faq"></a>FAQ
 -  적용의 실행에 실패한 경우 어떻게 되나요?  
실행에 실패한 경우 해당 시점에 Azure AD Connect가 내보내기 오류를 실행 중일 수 있습니다. 포털 페이지를 새로 고치고 다음 동기화 후에 다시 시도합니다. 기본 동기화 주기는 30분입니다. 

 -  **기존 개체**가 삭제할 개체여야 하는 경우 어떻게 하나요?  
이 경우에 기존 개체가 삭제되어야 하면 프로세스에는 원본 앵커의 변경 내용이 포함되지 않습니다. 온-프레미스 AD에서 해결할 수 있어야 합니다.  

 -  수정을 적용할 수 있기 위한 사용자의 사용 권한은 무엇인가요?  
RBAC 설정에서 전역 관리자 또는 기여자는 진단 및 문제 해결 프로세스에 액세스할 수 있는 권한이 있습니다.

 -  이 기능에 대해 AAD Connect를 구성하거나 Azure AD Connect Health 에이전트를 업데이트해야 하나요?  
아니요, 진단 프로세스는 전체 클라우드 기반 기능입니다.

 -  기존 개체가 일시 삭제되면 진단 프로세스는 개체가 다시 활성화되도록 복원하나요?  
아니요, 수정은 원본 앵커 이외의 개체 특성을 업데이트하지 않습니다. 
