---
title: Azure AD에서 응급 액세스 관리 계정의 관리 | Microsoft Docs
description: 이 문서에서는 조직이 기존 Azure Active Directory 환경 내에서 권한 있는 액세스를 제한할 수 있도록 응급 액세스 계정을 사용하는 방법을 설명합니다.
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 5c256befba4c73527b529c953f7b9cd4d266450c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Azure AD에서 응급 액세스 관리 계정의 관리 

대부분의 일상적인 작업의 경우 *전역 관리자* 권한은 사용자에게 필요하지 않습니다. 사용자가 보유해야 하는 것보다 더 높은 사용 권한이 필요한 작업을 실수로 수행할 수도 있으므로 사용자는 역할을 영구적으로 할당받아서는 안 됩니다. 사용자가 전역 관리자로 작동할 필요가 없는 경우, 자신의 계정 또는 다른 관리 계정에서 Azure AD(Azure Active Directory) PIM(Privileged Identity Management)을 사용하여 역할 할당을 활성화해야 합니다.

사용자가 스스로 관리자 액세스 권한을 취하는 것 외에도, 기존 개인 사용자의 계정을 관리자로 로그인하거나 활성화할 수 없으므로 실수로 Azure AD 테넌트를 관리하지 못하게 되는 것도 방지해야 합니다. 테넌트에 두 개 이상의 *응급 액세스 계정*을 저장하여 부주의한 관리 액세스 권한 부족의 영향을 줄일 수 있습니다.

응급 액세스 계정은 조직이 기존 Azure Active Directory 환경 내에서 권한 있는 액세스를 제한하는 데 유용합니다. 이러한 계정은 높은 권한을 부여받았으며 특정 개인에 할당되지 않습니다. 응급 액세스 계정은 일반 관리 계정은 사용될 수 없는 상황인 응급 또는 ‘유리 파손’ 시나리오로 제한됩니다. 조직은 응급 계정 사용을 오직 필요한 시간으로만 제한하는 목표를 유지해야 합니다.

조직은 다음과 같은 상황에서 응급 액세스 계정을 사용해야 할 수도 있습니다.

 - 사용자 계정이 페더레이션되고, 페더레이션이 현재 셀 네트워크 중단 또는 ID 공급자 중단으로 인해 사용할 수 없습니다. 예를 들어, 사용자 환경에서 ID 공급자 호스트가 중단된 경우 사용자는 Azure AD가 해당 ID 공급자로 리디렉션할 때 로그인하지 못할 수 있습니다. 
 - 관리자는 Azure Multi-Factor Authentication을 통해 등록되며 모든 개별 장치는 사용할 수 없습니다. 사용자는 역할을 활성화하는 Multi-Factor Authentication을 완료하지 못할 수도 있습니다. 예를 들어 셀 네트워크 장애로 인해 해당 장치에 대해 등록된 단 두 개의 인증 메커니즘인 수신 전화에 응답 및 문자 메시지 수신이 불가능할 수 있습니다. 
 - 최신 전역 관리자 액세스를 가진 사용자가 조직을 떠났습니다. Azure AD는 마지막 *전역 관리자* 계정이 삭제되지 않도록 할 수 있으나, 계정이 온-프레미스에서 삭제되거나 비활성되는 것은 방지할 수 없습니다. 각 상황에서 조직은 계정을 복구하지 못할 수 있습니다.

## <a name="initial-configuration"></a>초기 구성

두 개 이상의 응급 액세스 계정을 만듭니다. 이러한 계정은 \*.onmicrosoft.com 도메인을 사용하고, 온-프레미스 환경에서 페더레이션되거나 동기화되지 않은 클라우드 전용 계정이어야 합니다. 

계정은 조직의 개별 사용자와 연결되지 않아야 합니다. 조직은 이러한 계정의 자격 증명이 안전한지, 이를 사용하도록 인증된 개인에게만 알려져 있는지 확인해야 합니다. 

> [!NOTE]
> 한 응급 액세스 계정에 대한 계정 암호는 일반적으로 두세 개 부분으로 나뉘는데, 각기 다른 종이에 써서 각기 다른 위치에 있는 안전한 내화 금고에 보관합니다. 
>
> 응급 액세스 계정은 모든 직원 제공 휴대폰, 개별 직원이 가지고 다니는 하드웨어 토큰 또는 기타 직원별 자격 증명에 연결되지 않도록 해야 합니다. 이 예방 조치는 개별 직원이 자격 증명이 필요한 경우 접근할 수 없는 인스턴스를 포함합니다. 

### <a name="initial-configuration-with-permanent-assignments"></a>영구 할당된 초기 구성

한 가지 옵션은 사용자를 *전역 관리자* 역할의 영구 멤버로 만드는 것입니다. 이 옵션은 Azure AD Premium P2 구독이 없는 조직에 적합합니다.

손상된 암호로 인한 공격 위험을 줄이기 위해 Azure AD는 모든 개별 사용자에 대해 Multi-Factor Authentication을 요구할 것을 권장합니다. 이 그룹에는 공격에 노출된 계정이 상당한 영향을 미치는 관리자 및 다른 모든 사용자(예: 회계 담당자)가 포함됩니다. 

그러나 조직에 공유 장치가 없는 경우 Multi-Factor Authentication은 이러한 응급 액세스 계정에 사용하지 못할 수도 있습니다. Azure AD 및 기타 연결된 SaaS(Software as a Service) 앱의 [모든 관리자에 대해 Multi-Factor Authentication 등록](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)이 필요한 조건부 액세스 정책을 구성하는 경우 이 요구 사항에서 응급 액세스 계정을 제외하는 정책 제외 사항을 구성해야 할 수도 있습니다.

### <a name="initial-configuration-with-approvals"></a>승인이 필요한 초기 구성

다른 옵션은 적합한 사용자 및 승인자를 *전역 관리자* 역할을 활성화하도록 구성하는 것입니다. 이 옵션은 조직에 Azure AD Premium P2 구독이 있어야 합니다. 또한 여러 사람 및 네트워크 환경 간 공유 사용에 적합한 Multi-Factor Authentication 옵션이 필요합니다. 이러한 요구 사항은 *전역 관리자* 역할 활성화로 인해 사용자가 사전에 Multi-Factor Authentication을 수행해야 합니다. 자세한 내용은 [Azure AD Privileged Identity Management에서 Multi-Factor Authentication을 요구하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa)을 참조하세요.

응급 액세스 계정에 대해 개인 장치와 관련된 Multi-Factor Authentication을 사용하는 것은 좋지 않습니다. 실제 긴급 상황에서는 Multi-Factor Authentication에 등록된 장치에 액세스해야 하는 사람에게 해당 개인 장치가 없을 수도 있습니다. 

또한 위협 환경을 고려합니다. 예를 들어 자연 재해 사태와 같이 예측할 수 없는 상황이 발생하여 휴대폰 또는 다른 네트워크를 사용하지 못할 수도 있습니다. 등록된 모든 장치가 Azure AD와 통신하는 여러 수단이 있는 안전하고 알려진 위치에 있는지 확인하는 것이 중요합니다.

## <a name="ongoing-monitoring"></a>지속적인 모니터링

응급 액세스 계정의 모든 로그인 및 감사 활동에 대한 [Azure AD 로그인 및 감사 로그](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)를 모니터링합니다. 일반적으로 그러한 계정은 로그인하거나 변경해서는 안 되므로 해당 계정의 사용은 비정상적일 수 있으며 보안 확인이 요구됩니다.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>정기적으로 계정 검사의 유효성 검사 실행

계정의 유효성 검사를 위해 최소한으로 다음과 같은 단계를 수행합니다.
- 90일마다.
- 작업 변경, 사직 또는 신규 채용과 같이 IT 직원에 최근 변동이 있는 경우.
- 조직에서 Azure AD 구독이 변경된 경우.

응급 액세스 계정을 사용할 수 있도록 직원을 교육하려면 다음을 수행합니다.

* 보안 모니터링 직원이 계정 검사 작업이 진행 중임을 알고 있는지 확인합니다.
* 클라우드 사용자 계정이 자신의 역할에 로그인하고 활성화할 수 있는지 및 응급 시 이러한 단계를 수행해야 하는 사용자가 프로세스에 대해 교육받았는지 확인합니다.
* 개인 사용자의 장치 또는 개인 정보에 Multi-Factor Authentication 또는 SSPR(셀프 서비스 암호 재설정)을 등록하지 않았는지 확인합니다. 
* 장치에 Multi-Factor Authentication을 등록한 계정의 경우, 역할을 활성화하는 동안 사용하도록 하려면 응급 시 사용해야 할 수도 있는 모든 관리자가 장치에 액세스할 수 있는지 확인합니다. 또한 일반적인 오류 모드를 공유하지 않는 두 개 이상의 메커니즘을 통해 장치가 등록되어 있는지 확인합니다. 예를 들어 장치는 시설의 무선 네트워크 및 휴대 전화 공급자 네트워크 모두를 통해 인터넷과 통신할 수 있습니다.
* 계정 자격 증명을 업데이트합니다.

## <a name="next-steps"></a>다음 단계
- [클라우드 기반 사용자를 추가](add-users-azure-active-directory.md)하고 [전역 관리자 역할에 새 사용자를 할당](active-directory-users-assign-role-azure-portal.md)합니다.
- 아직 등록하지 않은 경우 [Azure Active Directory Premium에 등록](active-directory-get-started-premium.md)합니다.
- [관리자로 할당된 개별 사용자의 Azure Multi-Factor Authentication이 필요](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)합니다.
- Office 365를 사용하는 경우 [Office 365의 전역 관리자에 대한 추가 보호를 구성](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560)합니다.
- [전역 관리자는 액세스 검토를 수행](active-directory-privileged-identity-management-how-to-start-security-review.md)하고 [기존 전역 관리자를 더 구체적인 관리자 역할로 전환](active-directory-assign-admin-roles-azure-portal.md)합니다.


