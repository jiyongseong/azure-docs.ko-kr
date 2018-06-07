---
title: Azure Active Directory ID 보호 플레이 북 | Microsoft Docs
description: Azure AD ID 보호를 사용하여 손상된 ID 및 장치를 악용하는 공격자의 능력을 제한하고 이전에 손상이 우려되거나 손상된 ID 또는 장치를 보호할 수 있는 방법을 알아봅니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 응용 프로그램 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 29d8afb54ffbbed2178c8af611c0c0bb77fbe915
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory ID 보호 플레이 북

이 플레이 북은 다음 작업을 수행하는 데 도움이 됩니다.

* 위험 이벤트 및 취약성을 시뮬레이트하여 ID 보호 환경에 데이터 채우기
* 위험 기반 조건부 액세스 정책을 설정하고 이러한 정책의 영향 테스트


## <a name="simulating-risk-events"></a>위험 이벤트 시뮬레이션

이 섹션에서는 다음 위험 이벤트 유형을 시뮬레이트하는 단계를 제공합니다.

* 익명 IP 주소에서 로그인(초급)
* 잘 모르는 위치에서 로그인(중급)
* 비정상적 위치로 불가능한 이동(고급)

안전한 방법으로 다른 위험 이벤트를 시뮬레이션할 수 없습니다.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>익명 IP 주소에서 로그인

이 위험 이벤트에 대한 자세한 내용은 [익명 IP 주소에서 로그인](active-directory-reporting-risk-events.md#sign-ins-from-anonymous-ip-addresses)을 참조하세요. 

다음 절차를 완료하려면 다음을 사용해야 합니다.

- [Tor 브라우저](https://www.torproject.org/projects/torbrowser.html.en) - 익명 IP 주소를 시뮬레이션할 수 있습니다. 조직이 Tor 브라우저를 사용하여 제한하는 경우 가상 머신을 사용해야 합니다.
- 아직 Multi-Factor Authentication에 등록되어 있지 않은 테스트 계정

**익명 IP에서 로그인을 시뮬레이트하려면 다음 단계를 수행합니다.**

1. [Tor 브라우저](https://www.torproject.org/projects/torbrowser.html.en)를 사용하여 [https://myapps.microsoft.com](https://myapps.microsoft.com)로 이동합니다.   
2. **익명 IP 주소에서 로그인** 보고서에 표시하려는 계정의 자격 증명을 입력합니다.

로그인이 10 ~ 15분 이내에 ID 보호 대시보드에 나타납니다. 

### <a name="sign-ins-from-unfamiliar-locations"></a>알 수 없는 위치에서 로그인

이 위험 이벤트에 대한 자세한 내용은 [알 수 없는 위치에서 로그인](active-directory-reporting-risk-events.md#sign-in-from-unfamiliar-locations)을 참조하세요. 

알 수 없는 위치를 시뮬레이트하려면 테스트 계정이 이전에 로그인하지 않은 위치 및 장치에서 로그인해야 합니다.

다음 프로시저는 새로 만든 항목을 사용합니다.

- VPN 연결: 새 위치를 시뮬레이트합니다.

- 가상 머신: 새 장치를 시뮬레이트합니다.

다음 절차를 완료하려면 다음 항목을 포함하는 사용자 계정을 사용해야 합니다.

- 최소 30일 로그인 기록.
- Multi-Factor Authentication 사용.


**익숙하지 않은 위치에서 로그인을 시뮬레이트하려면 다음 단계를 수행합니다.**

1. 테스트 계정을 사용하여 로그인하는 경우 MFA 챌린지가 전달되지 않아 MFA 챌린지에 실패합니다.
2. 새 VPN을 사용하여 [https://myapps.microsoft.com](https://myapps.microsoft.com)으로 이동하고 테스트 계정의 자격 증명을 입력합니다.
   

로그인이 10 ~ 15분 이내에 ID 보호 대시보드에 나타납니다.

### <a name="impossible-travel-to-atypical-location"></a>비정상적 위치로 불가능한 이동

이 위험 이벤트에 대 한 자세한 내용은 [비정상적 위치로 이동 불가능](active-directory-reporting-risk-events.md#impossible-travel-to-atypical-locations)을 참조하세요. 

알고리즘이 기계 학습을 사용하여 익숙한 장치에서 불가능한 이동 또는 디렉터리의 다른 사용자가 사용하는 VPN에서 시도한 로그인 등 가양성을 걸러내기 때문에 불가능한 이동 조건을 시뮬레이트하는 작업은 어렵습니다. 또한 알고리즘은 위험 이벤트의 생성을 시작하기 전에 사용자에 대한 14일 간 및 10회의 로그인 기록이 필요합니다. 복잡한 Machine Learning 모델 및 위의 규칙으로 인해 다음 단계가 위험 이벤트를 유발하지 않을 가능성이 높습니다. 여러 Azure AD 계정이 이 위험 이벤트를 게시하도록 하기 위해 이러한 단계를 복제할 수도 있습니다.


**비정상적 위치로 불가능한 이동을 시뮬레이트하려면 다음 단계를 수행합니다.**

1. 표준 브라우저를 사용하여 [https://myapps.microsoft.com](https://myapps.microsoft.com)으로 이동합니다.  
2. 불가능한 이동 위험 이벤트를 생성하려는 계정의 자격 증명을 입력합니다.
3. 사용자 에이전트를 변경합니다. 사용자 에이전트 전환기 추가 기능을 사용하여 개발자 도구에서 Internet Explorer의 사용자 에이전트를 변경하거나 Firefox 또는 Chrome에서 사용자 에이전트를 변경할 수 있습니다.
4. 사용자의 IP 주소를 변경합니다. VPN, Tor 추가 기능을 사용하거나 다른 데이터 센터의 Azure에서 새 컴퓨터를 스핀업하여 사용자의 IP 주소를 변경할 수 있습니다.
5. 이전과 동일한 자격 증명을 사용하여 이전 로그인 후 몇 분 내에 [https://myapps.microsoft.com](https://myapps.microsoft.com)에 로그인합니다.

로그인이 2-4시간 이내에 ID 보호 대시보드에 나타납니다.

## <a name="simulating-vulnerabilities"></a>취약점 시뮬레이션
취약점은 잘못된 행위자에 의해 악용될 수 있는 Azure AD 환경의 단점입니다. 현재 3가지 유형의 취약점은 Azure AD의 다른 기능을 활용하는 Azure AD ID 보호에 표시됩니다. 이러한 기능이 설정되면 이러한 취약성이 ID 보호 대시보드에 자동으로 표시됩니다.

* Azure AD [Multi-Factor Authentication](authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](manage-apps/cloud-app-discovery.md).
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 


## <a name="testing-security-policies"></a>보안 정책 테스트

이 섹션에서는 사용자 위험과 로그인 위험 보안 정책을 테스트 하기 위한 단계를 제공합니다.


### <a name="user-risk-security-policy"></a>사용자 위험 보안 정책

자세한 내용은 [사용자 위험 보안 정책](active-directory-identityprotection.md#user-risk-security-policy)을 참조하세요.

![사용자 위험](./media/active-directory-identityprotection-playbook/02.png "플레이북")


**사용자 위험 보안 정책을 테스트하려면 다음 단계를 수행합니다.**

1. 테넌트에 대한 전역 관리자 자격 증명을 사용하여 [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.
2. **ID 보호**로 이동합니다. 
3. **Azure AD ID 보호** 페이지에서 **사용자 위험 정책**을 클릭합니다.
4. **할당** 섹션에서 원하는 사용자(및 그룹)를 선택하고 사용자 위험 수준을 선택합니다.

    ![사용자 위험](./media/active-directory-identityprotection-playbook/03.png "플레이북")

5. 컨트롤 섹션에서 원하는 액세스 제어를 선택합니다(예: 암호를 변경해야 함).
5. **정책 적용**에 **끄기**를 선택합니다.
6. 예를 들어 위험 이벤트 중 하나를 몇 번 시뮬레이트하여 테스트 계정의 사용자 위험을 상승시킵니다.
7. 몇 분 정도 기다린 다음, 사용자에 대한 해당 사용자 수준이 보통인지 확인합니다. 보통이 아닌 경우 사용자에 대해 더 많은 위험 이벤트를 시뮬레이트합니다.
8. **정책 적용**에 **켜기**를 선택합니다.
9. 이제 위험 수준이 상승한 사용자로 로그인하여 사용자 위험 기반 조건부 액세스를 테스트할 수 있습니다.
    
    

### <a name="sign-in-risk-security-policy"></a>로그인 위험 보안 정책

자세한 내용은 [사용자 위험 보안 정책](active-directory-identityprotection.md#user-risk-security-policy)을 참조하세요.

![로그인 위험](./media/active-directory-identityprotection-playbook/01.png "플레이북")


**로그인 위험 정책을 테스트하려면 다음 단계를 수행합니다.**

1. 테넌트에 대한 전역 관리자 자격 증명을 사용하여 [https://portal.azure.com](https://portal.azure.com)에 로그인합니다.

2. **Azure AD ID 보호**로 이동합니다.

3. **Azure AD ID 보호** 주 페이지에서 **로그인 위험 정책**을 클릭합니다. 

4. **할당** 섹션에서 원하는 사용자(및 그룹)를 선택하고 로그인 위험 수준을 선택합니다.

    ![로그인 위험](./media/active-directory-identityprotection-playbook/04.png "플레이북")


5. **컨트롤** 섹션에서 원하는 액세스 제어(예를 들어 **Multi-Factor Authentication 기능 필요**)를 선택합니다. 

6. **정책 적용**에 **켜기**를 선택합니다.

7. **저장**을 클릭합니다.

8. 이제 위험한 세션을 사용하여 로그인하여(예를 들어 Tor 브라우저 사용) 로그인 위험 기반 조건부 액세스를 테스트할 수 있습니다. 

 




## <a name="see-also"></a>참고 항목

- [Azure Active Directory ID 보호](active-directory-identityprotection.md)

