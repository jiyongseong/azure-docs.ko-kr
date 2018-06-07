---
title: Azure Active Directory의 조건부 액세스 모범 사례 | Microsoft Docs
description: 조건부 액세스 정책을 구성할 때 알아야 할 사항과 수행하지 않아야 할 사항에 대해 자세히 알아봅니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 8d3fa9bf11cd1c14b2d7ef37544cb5e043e2d9da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Azure Active Directory의 조건부 액세스 모범 사례

[Azure AD(Azure Active Directory) 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 사용하여 권한 있는 사용자가 클라우드 앱에 액세스하는 방법을 제어할 수 있습니다. 이 문서에서는 다음에 대한 정보를 제공합니다.

- 알아야 할 사항 
- 조건부 액세스 정책을 구성할 때 수행하지 말아야 할 사항. 

이 문서에서는 [Azure Active Directory의 조건부 액세스](active-directory-conditional-access-azure-portal.md)에 간략히 나온 개념 및 용어에 익숙하다고 가정합니다.



## <a name="whats-required-to-make-a-policy-work"></a>정책을 작동하는 데 무엇이 필요한가요?

새 정책을 만들 경우 선택된 사용자, 그룹, 앱 또는 액세스 제어가 없습니다.

![클라우드 앱](./media/active-directory-conditional-access-best-practices/02.png)


정책이 작동되려면 다음을 구성해야 합니다.


|대상           | 방법                                  | 이유|
|:--            | :--                                  | :-- |
|**클라우드 앱** |앱을 하나 이상 선택해야 합니다.  | 조건부 액세스 정책의 목표는 권한 있는 사용자가 클라우드 앱에 액세스하는 방법을 제어할 수 있도록 하는 것입니다.|
| **사용자 및 그룹** | 선택한 클라우드 앱에 액세스할 수 있도록 허가된 사용자 또는 그룹을 하나 이상 선택해야 합니다. | 할당된 사용자와 그룹이 없는 조건부 액세스 정책은 트리거되지 않습니다. |
| **액세스 제어** | 하나 이상의 액세스 제어를 선택해야 합니다. | 조건이 충족될 경우 정책 프로세서는 수행할 작업을 알고 있어야 합니다.|




## <a name="what-you-should-know"></a>알아야 할 사항

### <a name="how-are-assignments-evaluated"></a>할당은 어떻게 평가됩니까?

모든 할당은 논리적으로 **and**가 적용됩니다. 하나를 초과하여 할당을 구성한 경우 모든 할당이 충족되어야 정책을 트리거할 수 있습니다.  

조직의 네트워크 외부에서 수행되는 모든 연결에 적용되는 위치 조건을 구성해야 하는 경우:

- **모든 위치** 포함
- **모든 신뢰할 수 있는 IP** 제외


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Azure AD 관리 포털이 잠긴 경우 어떻게 합니까?

조건부 액세스 정책의 잘못된 설정으로 인해 Azure AD 포털이 잠긴 경우:

- 아직 차단되지 않은 조직 내 다른 관리자가 있는지 확인합니다. Azure Portal에 액세스할 수 있는 관리자는 로그인에 영향을 주는 정책을 비활성화할 수 있습니다. 

- 정책을 업데이트할 수 있는 조직의 관리자가 없는 경우 지원 요청을 제출해야 합니다. Microsoft 지원은 액세스를 방지하는 조건부 액세스 정책을 검토하고 업데이트할 수 있습니다.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Azure 클래식 포털과 Azure Portal에서 정책을 구성하면 어떻게 됩니까?  

두 정책은 모두 Azure Active Directory에서 적용되며 모든 요구 사항을 충족하는 경우에만 사용자가 액세스 권한을 획득할 수 있습니다.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Intune Silverlight 포털과 Azure Portal에 정책이 있으면 어떻게 됩니까?

두 정책은 모두 Azure Active Directory에서 적용되며 모든 요구 사항을 충족하는 경우에만 사용자가 액세스 권한을 획득할 수 있습니다.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>동일한 사용자에 대해 여러 정책을 구성하면 어떻게 됩니까?  

Azure Active Directory는 모든 로그인에 대해 모든 정책을 평가하고, 사용자에게 액세스 권한을 부여하기 전에 모든 요구 사항이 충족되는지 확인합니다.


### <a name="does-conditional-access-work-with-exchange-activesync"></a>조건부 액세스가 Exchange ActiveSync에서 작동합니까?

예, 조건부 액세스 정책에서 Exchange ActiveSync를 사용할 수 있습니다.






## <a name="what-you-should-avoid-doing"></a>금지해야 할 기능

조건부 액세스 프레임 워크는 훌륭한 구성 유연성을 제공합니다. 그러나 훌륭한 유연성은 잘못된 결과를 방지하기 위해 해제하기 전에 각 구성 정책을 신중하게 검토해야 한다는 것을 의미하기도 합니다. 이 컨텍스트에서 **모든 사용자 / 그룹 / 응용 프로그램**등 완전한 집합에 영향을 미치는 할당에 특별한 주의를 기울여야 합니다.

사용자 환경에서 다음과 같은 구성을 피해야 합니다.


**모든 사용자에 대한 모든 클라우드 앱:**

- **액세스 차단** - 이 구성은 사용자 전체 조직을 차단하므로 사용하지 않는 것이 좋습니다.

- **호환 장치가 필요** - 장치를 아직 등록하지 않은 사용자의 경우 이 정책은 Intune 포털에 대한 액세스를 비롯한 모든 액세스를 차단합니다. 등록된 장치가 없는 관리자의 경우 이 정책은 정책을 변경하기 위해 Azure Portal로 다시 돌아가지 않도록 차단합니다.

- **도메인 가입 필요** - 이 정책 차단 액세스에도 도메인에 가입된 장치가 아직 없는 경우에 조직의 모든 사용자에 대한 액세스를 차단할 가능성이 있습니다.


**모든 사용자 경우 모든 클라우드 앱, 모든 장치 플랫폼은 다음과 같습니다.**

- **액세스 차단** - 이 구성은 사용자 전체 조직을 차단하므로 사용하지 않는 것이 좋습니다.


## <a name="how-should-you-deploy-a-new-policy"></a>새 정책을 배포하려면 어떻게 해야 합니까?

첫 단계로, [what if(가정) 도구](active-directory-conditional-access-whatif.md)를 사용하여 정책을 평가해야 합니다.

새 정책을 환경에 배포할 준비가 된 경우 단계별로 수행해야 합니다.

1. 일부 사용자 집합에 정책을 적용하고 예상 대로 작동하는지 확인합니다. 

2.  더 많은 사용자를 포함하도록 정책을 확장하면 정책에서 모든 관리자를 계속 제외합니다. 이렇게 하면 관리자에게 여전히 액세스 권한이 있는지 확인하고, 변경해야 하는 경우 정책을 업데이트할 수 있습니다.

3. 실제로 필요한 경우에만 모든 사용자에게 정책을 적용합니다. 

모범 사례로, 다음과 같은 사용자 계정을 만듭니다.

- 정책 관리 전용 
- 모든 정책에서 제외


## <a name="policy-migration"></a>정책 마이그레이션

Azure Portal에서 만들지 않은 정책을 마이그레이션하는 것을 고려해야 하는 이유는 다음과 같습니다.

- 이전에는 처리할 수 없었던 시나리오를 이제 처리할 수 있습니다.

- 통합하여 관리해야 하는 정책의 수를 줄일 수 있습니다.   

- 중앙의 단일 위치에서 모든 조건부 액세스 정책을 관리할 수 있습니다.

- Azure 클래식 포털의 사용이 중지됩니다.   


자세한 내용은 [Azure Portal에서 클래식 정책 마이그레이션](active-directory-conditional-access-migration.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

조건부 액세스 정책을 구성하는 방법을 알아보려면 [Azure Active Directory에서 조건부 액세스 시작](active-directory-conditional-access-azure-portal-get-started.md)을 참조하세요.
