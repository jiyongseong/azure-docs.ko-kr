---
title: 셀프 서비스 암호 재설정 보고서 - Azure Active Directory
description: Azure AD 셀프 서비스 암호 재설정 이벤트에 대한 보고
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 45183e51e9071647e07cfd0738522f0dfa28bd7b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2018
ms.locfileid: "33869005"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Azure AD 암호 관리에 대한 보고 옵션

배포 후에 여러 조직에서는 SSPR(셀프 서비스 암호 다시 설정)을 실제로 사용하는 방법 및 경우를 알아보려고 합니다. Azure AD(Azure Active Directory)에서 제공하는 보고 기능은 미리 작성된 보고서를 사용하여 질문에 대답하도록 도와줍니다. 적절히 라이선스를 받은 경우 사용자 지정 쿼리를 만들 수도 있습니다.

![보고][Reporting]

[Azure Portal](https://portal.azure.com/):에 있는 보고서를 통해 다음과 같은 질문에 대답할 수 있습니다.

> [!NOTE]
> 사용자는 [전역 관리자](../active-directory-assign-admin-roles-azure-portal.md)여야 하며 조직을 대신하여 이 데이터를 수집하기 위해 옵트인해야 합니다. 옵트인하려면 적어도 한 번 **보고** 탭이나 감사 로그를 방문해야 합니다. 그때까지 조직을 위해 데이터가 수집되지 않습니다.
>

* 얼마나 많은 사람들이 암호 재설정을 위해 등록합니까?
* 누가 암호 재설정을 위해 등록합니까?
* 사람들이 등록하는 데이터는 무엇입니까?
* 지난 7일 동안 얼마나 많은 사람들이 자신의 암호를 재설정했습니까?
* 사용자나 관리자가 자신의 암호를 재설정하는 가장 일반적인 방법은 무엇인가요?
* 암호 재설정을 시도할 때 사용자나 관리자가 직면하는 공통적인 문제는 무엇인가요?
* 어느 관리자가 자신의 암호를 자주 재설정합니까?
* 암호 재설정에 대해 의심스러운 활동이 있습니까?

## <a name="power-bi-content-pack"></a>Power BI 콘텐츠 팩

Power BI 사용자의 경우 Azure AD용 콘텐츠 팩에 SSPR에 대한 사용하기 쉬운 보고 기능이 포함되어 있습니다. 콘텐츠 팩을 사용하고 배포하는 방법에 대한 자세한 내용은 [Azure Active Directory Power BI 콘텐츠 팩 사용 방법](../active-directory-reporting-power-bi-content-pack-how-to.md)을 참조하세요. 콘텐츠 팩을 사용하면 자신의 대시보드를 만들어서 조직의 다른 사람들과 공유할 수 있습니다.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Azure Portal에서 암호 관리 보고서를 보는 방법

Azure Portal 환경에서 암호 재설정 및 암호 재설정 등록 활동을 볼 수 있는 향상된 방법이 있습니다. 다음 단계에 따라 암호 재설정 및 암호 재설정 등록 이벤트를 확인합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 왼쪽 창에서 **모든 서비스**를 선택합니다.
3. 서비스 목록에서 **Azure Active Directory**를 검색하고 선택합니다.
4. **사용자 및 그룹**을 선택합니다.
5. **사용자 및 그룹** 메뉴에서 **감사 로그**를 선택합니다. 그러면 디렉터리의 모든 사용자에 대해 발생한 감사 이벤트가 모두 표시됩니다. 이 보기를 필터링하여 암호 관련 이벤트를 모두 볼 수 있습니다.
6. 이 보기를 암호 재설정 관련 이벤트로만 필터링하려면 창 위쪽의 **필터** 단추를 선택합니다.
7. **필터** 메뉴에서 **범주** 드롭다운 목록을 선택하고, **셀프 서비스 암호 관리** 범주 유형으로 변경합니다.
8. 필요에 따라 관심 있는 특정 **활동**을 선택하여 목록을 추가로 필터링합니다.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Azure AD 보고서 및 이벤트 API에서 암호 관리 이벤트를 검색하는 방법

Azure AD 보고서 및 이벤트 API는 암호 재설정 및 암호 재설정 등록 보고서에 포함된 모든 정보를 검색하도록 지원하고 있습니다. 이 API를 사용하면 개별 암호 재설정 및 암호 재설정 등록 이벤트를 다운로드하여 선택한 보고 기술과 통합할 수 있습니다.

> [!IMPORTANT]
> 현재 Azure AD 보고서 및 이벤트 API는 [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) 및 [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) 유형의 최대 *75,000개의 개별 이벤트*를 검색합니다. API 범위는 지난 30일 동안입니다.
> 
> 이 창에서 미치지 않는 데이터를 검색하거나 저장해야 하는 경우 외부 데이터베이스에서 이 데이터를 유지하고 API를 사용하여 그 결과인 델타를 쿼리하는 것이 좋습니다. 조직에서 SSPR을 사용하기 시작할 때 이 데이터 검색을 시작하는 것이 좋습니다. 외부적으로 지속하면서, 해당 위치부터 계속 델타를 추적합니다.
>

### <a name="how-to-get-started-with-the-reporting-api"></a>보고 API를 시작하는 방법

이 데이터에 액세스하려면 작은 응용 프로그램 또는 스크립트를 작성하여 서버에서 검색해야 합니다. 자세한 내용은 [Azure AD 보고 API 시작](../active-directory-reporting-api-getting-started-azure-portal.md)을 참조하세요.

작동하는 스크립트가 있으면 시나리오에 맞게 검색할 수 있는 암호 재설정 및 등록 이벤트를 검사하려 합니다.

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): 암호 재설정 이벤트에 대해 사용 가능한 열을 나열합니다.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): 암호 재설정 등록 이벤트에 대해 사용 가능한 열을 나열합니다.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Azure Portal의 보고서 열 설명

다음 목록에서는 Azure Portal의 각 보고서의 열에 대해 자세히 설명합니다.

* **사용자**: 암호 재설정 등록 작업을 시도한 사용자입니다.
* **역할**: 디렉터리에서 사용자의 역할입니다.
* **날짜 및 시간**: 시도한 날짜 및 시간입니다.
* **등록된 데이터**: 암호 재설정 등록 중 사용자가 제공한 인증 데이터입니다.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Azure Portal의 보고서 값 설명

다음 표에서는 Azure Portal의 각 열에 대해 설정할 수 있는 다양한 값을 보여 줍니다.

| 열 | 허용되는 값과 해당 의미 |
| --- | --- |
| 등록된 데이터 |**대체 전자 메일**: 인증하는 데 사용자가 사용한 대체 전자 메일 또는 인증 전자 메일입니다.<p><p>**사무실 전화**: 인증하는 데 사용자가 사용한 사무실 전화입니다.<p>**휴대폰**: 인증하는 데 사용자가 사용한 휴대폰 또는 인증 전화입니다.<p>**보안 질문**: 인증하는 데 사용자가 사용한 보안 질문입니다.<p>**이전 방법 조합(예: 대체 전자 메일 + 휴대폰)**: 2개의 게이트 정책이 지정되고 암호 재설정 요청을 인증하는 데 사용자가 사용한 두 방법을 표시합니다. |

## <a name="self-service-password-management-activity-types"></a>셀프 서비스 암호 관리 활동 유형

다음 활동 유형은 **셀프 서비스 암호 관리** 감사 이벤트 범주에 표시됩니다.

* [셀프 서비스 암호 다시 설정에서 차단](#activity-type-blocked-from-self-service-password-reset): 사용자가 암호를 다시 설정하거나 특정 게이트를 사용하거나 24시간 이내에 총 6회 이상 전화 번호의 유효성을 검사하려고 했음을 나타냅니다.
* [암호 변경(셀프 서비스)](#activity-type-change-password-self-service): 사용자가 자발적으로 또는 강제로(만료로 인해) 암호 변경을 수행했음을 나타냅니다.
* [암호 재설정(관리자)](#activity-type-reset-password-by-admin): 관리자가 Azure Portal에서 사용자를 대신하여 암호를 재설정했음을 나타냅니다.
* [암호 재설정(셀프 서비스)](#activity-type-reset-password-self-service): 사용자가 [Azure AD 암호 재설정 포털](https://passwordreset.microsoftonline.com)에서 자신의 암호를 성공적으로 재설정했음을 나타냅니다.
* [셀프 서비스 암호 재설정 흐름 활동 진행률](#activity-type-self-serve-password-reset-flow-activity-progress): 암호 재설정 프로세스의 일부로 사용자가 진행하는 각각의 특정 단계(예: 특정 암호 재설정 인증 게이트 전달)를 나타냅니다.
* [사용자 계정 잠금 해제(셀프 서비스)](#activity-type-unlock-user-account-self-service): 사용자가 재설정 없이 Active Directory 계정 잠금 해제 기능을 사용하여 [Azure AD 암호 재설정 포털](https://passwordreset.microsoftonline.com)에서 자신의 암호를 재설정하지 않고 Active Directory 계정을 성공적으로 잠금 해제했음을 나타냅니다.
* [사용자가 셀프 서비스 암호 재설정 등록](#activity-type-user-registered-for-self-service-password-reset): 사용자가 현재 지정된 테넌트 암호 재설정 정책에 따라 자신의 암호를 재설정하는 데 필요한 모든 정보를 등록했음을 나타냅니다.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>활동 유형: 셀프 서비스 암호 다시 설정에서 차단

다음 목록에서는 이 활동에 대해 자세히 설명합니다.

* **활동 설명**: 사용자가 암호를 다시 설정하거나 특정 게이트를 사용하거나 24시간 이내에 총 6회 이상 전화 번호의 유효성을 검사하려고 했음을 나타냅니다.
* **활동 행위자**: 추가 다시 설정 작업을 수행할 수 없도록 제한된 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **활동 대상**: 추가 다시 설정 작업을 수행할 수 없도록 제한된 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **활동 상태**:
  * _성공_: 사용자가 추가 다시 설정을 수행하거나 추가 인증 방법을 시도하거나 다음 24시간 동안 전화 번호의 유효성을 추가로 검사할 수 없도록 제한되었음을 나타냅니다.
* **활동 상태 실패 이유**: 해당되지 않습니다.

### <a name="activity-type-change-password-self-service"></a>활동 유형: 암호 변경(셀프 서비스)

다음 목록에서는 이 활동에 대해 자세히 설명합니다.

* **활동 설명**: 사용자가 자발적으로 또는 강제로(만료로 인해) 암호 변경을 수행했음을 나타냅니다.
* **활동 행위자**: 자신의 암호를 변경한 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **활동 대상**: 자신의 암호를 변경한 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **활동 상태**:
  * _성공_: 사용자가 자신의 암호를 성공적으로 변경했음을 나타냅니다.
  * _실패_: 사용자가 자신의 암호를 변경하지 못했음을 나타냅니다. 행을 선택하여 **활동 상태 이유** 범주에서 실패한 원인에 대해 자세히 알아볼 수 있습니다.
* **활동 상태 실패 이유**: 
  * _FuzzyPolicyViolationInvalidPassword_: 사용자가 Microsoft의 차단 암호 검색 기능으로 인해 너무 흔하거나 특히 약한 것으로 식별되어 자동으로 차단되는 암호를 선택했습니다.

### <a name="activity-type-reset-password-by-admin"></a>활동 유형: 암호 다시 설정(관리자)

다음 목록에서는 이 활동에 대해 자세히 설명합니다.

* **활동 설명**: 관리자가 Azure Portal에서 사용자를 대신하여 암호를 재설정했음을 나타냅니다.
* **활동 행위자**: 다른 최종 사용자 또는 관리자를 대신하여 암호를 재설정한 관리자입니다. 전역 관리자, 암호 관리자, 사용자 관리자 또는 기술 지원팀 관리자여야 합니다.
* **활동 대상**: 자신의 암호를 다시 설정한 사용자입니다. 사용자는 최종 사용자 또는 다른 관리자일 수 있습니다.
* **활동 상태**:
  * _성공_: 관리자가 사용자의 암호를 성공적으로 다시 설정했음을 나타냅니다.
  * _실패_: 관리자가 사용자의 암호를 변경하지 못했음을 나타냅니다. 행을 선택하여 **활동 상태 이유** 범주에서 실패한 원인에 대해 자세히 알아볼 수 있습니다.

### <a name="activity-type-reset-password-self-service"></a>활동 유형: 암호 다시 설정(셀프 서비스)

다음 목록에서는 이 활동에 대해 자세히 설명합니다.

* **활동 설명** – 사용자가 [Azure AD 암호 재설정 포털](https://passwordreset.microsoftonline.com)에서 자신의 암호를 성공적으로 재설정했음을 나타냅니다.
* **활동 행위자**: 자신의 암호를 재설정한 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **활동 대상**: 자신의 암호를 재설정한 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **활동 상태**:
  * _성공_: 사용자가 자신의 암호를 성공적으로 재설정했음을 나타냅니다.
  * _실패_: 사용자가 자신의 암호를 재설정하는 데 실패했음을 나타냅니다. 행을 선택하여 **활동 상태 이유** 범주에서 실패한 원인에 대해 자세히 알아볼 수 있습니다.
* **활동 상태 실패 이유**: 
  * _FuzzyPolicyViolationInvalidPassword_: 관리자가 Microsoft의 차단 암호 검색 기능으로 인해 너무 흔하거나 특히 약한 것으로 식별되어 자동으로 차단되는 암호를 선택했습니다.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>활동 유형: 셀프 서비스 암호 다시 설정 흐름 활동 진행률

다음 목록에서는 이 활동에 대해 자세히 설명합니다.

* **활동 설명**: 암호 다시 설정 프로세스의 일부로 사용자가 진행하는 각각의 특정 단계(예: 특정 암호 다시 설정 인증 게이트 전달)를 나타냅니다.
* **활동 행위자**: 암호 다시 설정 흐름의 일부를 수행한 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **활동 대상**: 암호 다시 설정 흐름의 일부를 수행한 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **활동 상태**:
  * _성공_: 사용자가 암호 다시 설정 흐름의 특정 단계를 성공적으로 완료했음을 나타냅니다.
  * _실패_: 암호 다시 설정 흐름의 특정 단계가 실패했음을 나타냅니다. 행을 선택하여 **활동 상태 이유** 범주에서 실패한 원인에 대해 자세히 알아볼 수 있습니다.
* **활동 상태 이유**: [모든 허용되는 다시 설정 활동 상태 이유](#allowed-values-for-details-column)에 대해서는 다음 표를 참조하세요.

### <a name="activity-type-unlock-a-user-account-self-service"></a>활동 유형: 사용자 계정 잠금 해제(셀프 서비스)

다음 목록에서는 이 활동에 대해 자세히 설명합니다.

* **활동 설명**: 사용자가 재설정 없이 Active Directory 계정 잠금 해제 기능을 사용하여 [Azure AD 암호 재설정 포털](https://passwordreset.microsoftonline.com)에서 자신의 암호를 재설정하지 않고 Active Directory 계정을 성공적으로 잠금 해제했음을 나타냅니다.
* **활동 행위자**: 자신의 암호를 재설정하지 않고 계정을 잠금 해제한 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **활동 대상**: 자신의 암호를 재설정하지 않고 계정을 잠금 해제한 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **허용되는 활동 상태**:
  * _성공_: 사용자가 자신의 계정을 성공적으로 잠금 해제했음을 나타냅니다.
  * _실패_: 사용자가 자신의 계정을 잠금 해제하지 못했음을 나타냅니다. 행을 선택하여 **활동 상태 이유** 범주에서 실패한 원인에 대해 자세히 알아볼 수 있습니다.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>활동 유형: 사용자가 셀프 서비스 암호 다시 설정 등록

다음 목록에서는 이 활동에 대해 자세히 설명합니다.

* **활동 설명**: 사용자가 현재 지정된 테넌트 암호 재설정 정책에 따라 자신의 암호를 재설정하는 데 필요한 모든 정보를 등록했음을 나타냅니다. 
* **활동 행위자**: 암호 다시 설정을 등록한 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **활동 대상**: 암호 다시 설정을 등록한 사용자입니다. 사용자는 최종 사용자 또는 관리자일 수 있습니다.
* **허용되는 활동 상태**:
  * _성공_: 사용자가 현재 정책에 따라 암호 재설정에 성공적으로 등록했음을 나타냅니다. 
  * _실패_: 사용자가 암호 다시 설정을 등록하지 못했음을 나타냅니다. 행을 선택하여 **활동 상태 이유** 범주에서 실패한 원인에 대해 자세히 알아볼 수 있습니다. 

     >[!NOTE]
     >실패라고 해서 사용자가 자신의 암호를 다시 설정할 수 없는 것은 아니며, 등록 프로세스를 완료하지 않은 것을 의미합니다. 올바른 계정에 확인되지 않은 데이터(예: 유효성이 검사되지 않은 전화 번호)가 있는 경우 이 전화 번호를 확인하지 않아도 해당 계정을 계속 사용하여 암호를 다시 설정할 수 있습니다. 자세한 내용은 [사용자가 등록하면 어떻게 되나요?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)를 참조하세요.
     >

## <a name="next-steps"></a>다음 단계

* [성공적인 SSPR 롤아웃을 어떻게 완료합니까?](howto-sspr-deployment.md)
* [암호 재설정 또는 변경](../active-directory-passwords-update-your-own-password.md)
* [셀프 서비스 암호 재설정 등록](../active-directory-passwords-reset-register.md)
* [라이선스 관련 질문이 있습니까?](concept-sspr-licensing.md)
* [SSPR에서 사용하는 데이터는 무엇이며, 사용자에 대해 어떤 데이터를 채워야 합니까?](howto-sspr-authenticationdata.md)
* [사용자가 사용할 수 있는 인증 방법은 무엇입니까?](concept-sspr-howitworks.md#authentication-methods)
* [SSPR에서 사용하는 정책 옵션은 무엇입니까?](concept-sspr-policy.md)
* [비밀번호 쓰기 저장은 무엇이며, 왜 관심을 가져야 합니까?](howto-sspr-writeback.md)
* [모든 SSPR 옵션과 그 의미는 무엇입니까?](concept-sspr-howitworks.md)
* [무엇인가 손상된 문제가 있습니다. SSPR 문제는 어떻게 해결합니까?](active-directory-passwords-troubleshoot.md)
* [다른 곳에서 다루지 않았던 질문이 있습니다.](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Azure AD의 SSPR 작업 감사 로그 예제"
