---
title: 액세스 패널 웹 사이트에 로그인할 때 발생하는 문제 | Microsoft Docs
description: 액세스 패널을 사용하여 로그인하려고 하는 동안 발생할 수 있는 문제를 해결하기 위한 지침
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviwer: japere
ms.openlocfilehash: 1820ab1e2295e6e0c7795c9d014d001d294bb337
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34069615"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>액세스 패널 웹 사이트에 로그인할 때 발생하는 문제

액세스 패널은 웹 기반 포털로 Azure AD(Azure Active Directory)에 회사 또는 학교 계정이 있는 사용자가 Azure AD 관리자를 통해 액세스 권한을 부여받은 클라우드 기반 응용 프로그램을 보고 시작할 수 있도록 합니다. 또한 Azure AD 버전의 사용자는 액세스 패널을 통해 셀프 서비스 그룹 및 앱 관리 기능을 사용할 수도 있습니다. 액세스 패널은 Azure Portal과 별개이며, 사용자에게 Azure 구독을 요구하지 않습니다.

사용자는 Azure AD에서 회사 또는 학교 계정이 있는 경우 액세스 패널에 로그인할 수 있습니다.

-   사용자는 Azure AD에서 직접 인증을 받을 수 있습니다.

-   사용자는 AD FS(Active Directory Federation Services)를 사용하여 인증을 받을 수 있습니다.

-   사용자는 Windows Server Active Directory에서 인증을 받을 수 있습니다.

사용자가 Azure 또는 Office 365에 대한 구독을 가지고 있고 Azure Portal 또는 Office 365 응용 프로그램을 사용하고 있으면, 다시 로그인할 필요 없이 액세스 패널을 사용할 수 있습니다. 인증되지 않은 사용자는 Azure AD 계정에 대한 사용자 이름과 암호를 사용하여 로그인하도록 요청받습니다. 조직에서 페더레이션을 구성한 경우 사용자 이름만 입력하면 됩니다.

## <a name="general-issues-to-check-first"></a>먼저 확인해야 할 일반적인 문제 

-   사용자가 **올바른 URL**(<https://myapps.microsoft.com>)에 로그인되어 있는지 확인합니다.

-   사용자의 브라우저에서 해당 **신뢰할 수 있는 사이트**에 URL을 추가했는지 확인합니다.

-   사용자의 계정이 로그인에 대해 **활성화**되었는지 확인합니다.

-   사용자의 계정이 **잠겨 있지 않는지** 확인합니다.

-   사용자의 **암호가 만료되거나 기억하지 못하는지** 확인합니다.

-   **Multi-Factor Authentication**에서 사용자 액세스를 차단하지 않는지 확인합니다.

-   **조건부 액세스 정책** 또는 **ID 보호** 정책이 사용자 액세스를 차단하지 않는지 확인합니다.

-   사용자의 **인증 연락처 정보**를 최신으로 유지하여 Multi-Factor Authentication 또는 조건부 액세스 정책을 적용할 수 있도록 해야 합니다.

-   브라우저의 쿠키를 지우고 다시 로그인하려고 시도할 수도 있는지 확인합니다.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>액세스 패널에 대한 브라우저 요구 사항 충족

액세스 패널에는 JavaScript를 지원하고 CSS를 사용하도록 설정한 브라우저가 필요합니다. 액세스 패널에서 암호 기반 SSO(Single Sign-On)를 사용하려면 사용자의 브라우저에 액세스 패널 확장이 설치되어 있어야 합니다. 사용자가 암호 기반 SSO에 구성된 응용 프로그램을 선택할 때 이 확장이 자동으로 다운로드됩니다.

암호 기반 SSO의 경우 최종 사용자 브라우저는 다음 중 하나일 수 있습니다.

-   Internet Explorer 8, 9, 10, 11 - Windows 7 이상

-   Windows 10 Anniversary Edition 이상 Edge 

-   Chrome - Windows 7 이상 및 Mac OS X 이상

-   Firefox 26.0 이상 - Windows XP SP2 이상 및 Mac OS X 10.6 이상


## <a name="problems-with-the-users-account"></a>사용자의 계정과 관련된 문제

사용자의 계정 문제로 인해 액세스 패널에 대한 액세스가 차단될 수 있습니다. 다음 몇 가지 방법으로 사용자 및 해당 계정 설정과 관련된 문제를 해결할 수 있습니다.

-   [Azure Active Directory에 사용자의 계정이 존재하는지 확인](#check-if-a-user-account-exists-in-azure-active-directory)

-   [사용자의 계정 상태 확인](#check-a-users-account-status)

-   [사용자의 암호 다시 설정](#reset-a-users-password)

-   [셀프 서비스 암호 재설정 사용](#enable-self-service-password-reset)

-   [사용자의 Multi-Factor Authentication 상태 확인](#check-a-users-multi-factor-authentication-status)

-   [사용자의 인증 연락처 정보 확인](#check-a-users-authentication-contact-info)

-   [사용자의 그룹 멤버 자격 확인](#check-a-users-group-memberships)

-   [사용자의 할당된 라이선스 확인](#check-a-users-assigned-licenses)

-   [사용자에게 라이선스 할당](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Azure Active Directory에 사용자의 계정이 존재하는지 확인

사용자의 계정이 있는지를 확인하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  사용자 개체의 속성이 예상한 대로 표시되고 데이터가 누락되지 않았는지 확인합니다.

### <a name="check-a-users-account-status"></a>사용자의 계정 상태 확인

사용자의 계정 상태를 확인하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **프로필**을 클릭합니다.

8.  **설정** 아래에서 **로그인 차단**이 **아니오**로 설정되어야 합니다.

### <a name="reset-a-users-password"></a>사용자의 암호 다시 설정

사용자의 암호를 다시 설정하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  사용자 창 맨 위에 있는 **암호 재설정** 단추를 클릭합니다.

8.  표시되는 **암호 재설정** 창에서 **암호 재설정** 단추를 클릭합니다.

9.  사용자를 위한 **임시 암호** 또는 **새 암호 입력**을 복사합니다.

10. 사용자에게 이 새 암호를 전달하고 다음 번에 Azure Active Directory에 로그인하는 동안 이 암호를 변경하도록 해야 합니다.

### <a name="enable-self-service-password-reset"></a>셀프 서비스 암호 재설정 사용

셀프 서비스 암호 재설정을 사용하려면 아래 배포 단계를 따르세요.

-   [사용자가 Azure Active Directory 암호를 재설정할 수 있도록 설정](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-their-azure-ad-passwords)

-   [사용자가 Active Directory 온-프레미스 암호를 재설정하거나 변경하도록 설정](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords)

### <a name="check-a-users-multi-factor-authentication-status"></a>사용자의 Multi-Factor Authentication 상태 확인

사용자의 Multi-Factor Authentication 상태를 확인하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  창 맨 위에 있는 **Multi-Factor Authentication** 단추를 클릭합니다.

7.  **Multi-factor Authentication 관리 포털**을 일단 로드하면 **사용자** 탭인지 확인합니다.

8.  검색, 필터링 또는 정렬을 사용하여 사용자 목록에서 사용자를 찾습니다.

9.  사용자 목록에서 사용자를 선택하고 원하는 대로 Multi-Factor Authentication을 **사용**, **사용 안 함** 또는 **적용**합니다.

   >[!NOTE]
   >사용자가 **강제된** 상태인 경우 일시적으로 **사용 안 함**으로 설정하여 해당 계정으로 다시 돌아올 수 있도록 할 수 있습니다. 다시 돌아오면 해당 상태를 **사용**으로 변경하여 다음 번에 로그인하는 도중 해당 연락처 정보를 다시 등록할 수 있습니다. 또는 [사용자의 인증 연락처 정보 확인](#check-a-users-authentication-contact-info)에 있는 단계를 수행하여 이 데이터를 확인하거나 설정할 수 있습니다.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>사용자의 인증 연락처 정보 확인

Multi-Factor Authentication, 조건부 액세스, ID 보호 및 암호 재설정에 사용되는 사용자의 인증 연락처 정보를 확인하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **프로필**을 클릭합니다.

8.  **인증 연락처 정보**까지 스크롤합니다.

9.  필요에 따라 사용자 및 업데이트에 등록된 데이터를 **검토**합니다.

### <a name="check-a-users-group-memberships"></a>사용자의 그룹 구성원 자격 확인

사용자의 그룹 구성원 자격을 확인하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **그룹**을 클릭하여 사용자가 구성원으로 참여하는 그룹을 확인합니다.

### <a name="check-a-users-assigned-licenses"></a>사용자의 할당된 라이선스 확인

사용자의 할당된 라이선스를 확인하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **라이선스**를 클릭하여 사용자가 현재 할당된 라이선스를 봅니다.

### <a name="assign-a-user-a-license"></a>사용자에게 라이선스 할당 

사용자에게 라이선스를 할당하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

5.  **모든 사용자**를 클릭합니다.

6.  관심이 있는 사용자를 **검색**하고 **행을 클릭**하여 선택합니다.

7.  **라이선스**를 클릭하여 사용자가 현재 할당된 라이선스를 봅니다.

8.  **할당** 단추를 클릭합니다.

9.  사용 가능한 제품 목록에서 **하나 이상의 제품**을 선택합니다.

10. **선택 사항** 제품을 상세하게 할당하려면 **할당 옵션** 항목을 클릭합니다. 완료되면 **확인**을 클릭합니다.

11. **할당** 단추를 클릭하여 이러한 라이선스를 이 사용자에게 할당합니다.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>이러한 문제 해결 단계로 문제가 해결되지 않는 경우

사용할 수 있는 경우 다음 정보로 지원 티켓을 엽니다.

-   상관 관계 오류 ID

-   UPN(사용자 전자 메일 주소)

-   테넌트 ID

-   브라우저 종류

-   오류가 발생하는 동안 표준 시간대 및 시간/기간

-   Fiddler 추적

## <a name="next-steps"></a>다음 단계
[응용 프로그램 프록시를 사용하여 앱에 Single Sign-On 제공](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
