---
title: 암호 Single Sign-On에 대해 구성된 Azure AD 갤러리 응용 프로그램에 로그인하는 문제 | Microsoft Docs
description: 암호 Single Sign-On에 대해 구성된 Azure AD 갤러리 응용 프로그램에 로그인하는 것과 관련된 문제를 해결하는 지침을 제공하는 문제 영역을 설명합니다.
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
ms.openlocfilehash: f13f9fa54af13dbbe435a846be35c9e06fe65455
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>암호 Single Sign-On에 대해 구성된 Azure AD 갤러리 응용 프로그램에 로그인하는 문제

액세스 패널은 웹 기반 포털로 Azure AD(Azure Active Directory)에 회사 또는 학교 계정이 있는 사용자가 Azure AD 관리자를 통해 액세스 권한을 부여받은 클라우드 기반 응용 프로그램을 보고 시작할 수 있도록 합니다. 또한 Azure AD 버전의 사용자는 액세스 패널을 통해 셀프 서비스 그룹 및 앱 관리 기능을 사용할 수도 있습니다. 액세스 패널은 Azure Portal과 별개이며, 사용자에게 Azure 구독을 요구하지 않습니다.

액세스 패널에서 암호 기반 SSO(Single Sign-On)를 사용하려면 사용자의 브라우저에 액세스 패널 확장이 설치되어 있어야 합니다. 사용자가 암호 기반 SSO에 구성된 응용 프로그램을 선택할 때 이 확장이 자동으로 다운로드됩니다.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>액세스 패널에 대한 브라우저 요구 사항 충족

액세스 패널에는 JavaScript를 지원하고 CSS를 사용하도록 설정한 브라우저가 필요합니다. 액세스 패널에서 암호 기반 SSO(Single Sign-On)를 사용하려면 사용자의 브라우저에 액세스 패널 확장이 설치되어 있어야 합니다. 사용자가 암호 기반 SSO에 구성된 응용 프로그램을 선택할 때 이 확장이 자동으로 다운로드됩니다.

암호 기반 SSO의 경우 최종 사용자 브라우저는 다음 중 하나일 수 있습니다.

-   Internet Explorer 8, 9, 10, 11 - Windows 7 이상

-   Chrome - Windows 7 이상 및 Mac OS X 이상

-   Firefox 26.0 이상 - Windows XP SP2 이상 및 Mac OS X 10.6 이상

>[!NOTE]
>브라우저 확장이 Edge에서 지원되는 경우 Windows 10의 Edge에 암호 기반 SSO 확장을 사용할 수 있습니다.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장을 설치하는 방법

액세스 패널 브라우저 확장을 설치하려면 아래 단계를 수행합니다.

1.  지원되는 브라우저 중 하나에서 [액세스 패널](https://myapps.microsoft.com)을 열고 Azure AD에서 **사용자**로 로그인합니다.

2.  액세스 패널에서 **암호-SSO 응용 프로그램**을 클릭합니다.

3.  소프트웨어를 설치하라는 프롬프트에서 **지금 설치**를 선택합니다.

4.  브라우저에 따라 다운로드 링크로 이동됩니다. 브라우저에 확장을 **추가**합니다.

5.  브라우저에서 요청하면 확장을 **사용** 또는 **허용**하도록 선택합니다.

6.  설치되면 브라우저 세션을 **다시 시작**합니다.

7.  액세스 패널에 로그인하고 암호 SSO 응용 프로그램을 **시작**할 수 있는지 확인합니다.

아래와 같은 직접 링크에서 Chrome 및 Firefox에 대한 확장을 다운로드할 수 있습니다.

-   [Chrome 액세스 패널 확장](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox 액세스 패널 확장](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Internet Explorer에 대한 그룹 정책 설정

사용자의 컴퓨터에 Internet Explorer용 액세스 패널 확장을 원격으로 설치할 수 있는 그룹 정책을 설정할 수 있습니다.

필수 구성 요소는 다음과 같습니다.

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)를 설정하고, 사용자 컴퓨터를 도메인에 가입시킨 상태여야 합니다.

-   그룹 정책 개체(GPO)를 편집하는 "설정 편집" 사용 권한이 있어야 합니다. 기본적으로 도메인 관리자, 엔터프라이즈 관리자 및 그룹 정책 작성자/소유자 보안 그룹의 멤버에게 이 권한이 있습니다. [자세히 알아보기](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

자습서에 따라 그룹 정책을 구성하고 사용자에게 배포하는 방법에 대한 단계별 지침은 [그룹 정책을 사용하여 Internet Explorer용 액세스 패널 확장을 배포하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)을 따릅니다.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Internet Explorer에서 액세스 패널 문제 해결

진단 도구 및 IE용 확장을 구성하는 방법에 대한 단계별 지침에 액세스하려면 [Internet Explorer의 액세스 패널 확장 문제 해결](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-Troubleshoot) 가이드를 따릅니다.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>비갤러리 응용 프로그램에 대해 암호 Single Sign-On을 구성하는 방법

Azure AD 갤러리에서 응용 프로그램을 구성하려면 다음을 수행해야 합니다.

-   [비갤러리 응용 프로그램 추가](#add-a-non-gallery-application)

-   [암호 Single Sign-On에 대한 응용 프로그램 구성](#configure-the-application-for-password-single-sign-on)

-   [응용 프로그램에 사용자 할당](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>비갤러리 응용 프로그램 추가

Azure AD 갤러리에서 응용 프로그램을 추가하려면 아래 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **엔터프라이즈 응용 프로그램** 창의 오른쪽 위 모서리에서 **추가** 단추를 클릭합니다.

6.  **비갤러리 응용 프로그램**을 클릭합니다.

7.  **이름** 텍스트 상자에 응용 프로그램의 이름을 입력합니다. **추가**를 선택합니다.

짧은 시간 후에 응용 프로그램의 구성 창을 볼 수 있습니다.

### <a name="configure-the-application-for-password-single-sign-on"></a>암호 Single Sign-On에 대한 응용 프로그램 구성

응용 프로그램에 Single Sign-On을 구성하려면 아래 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 또는 **공동 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

   * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  Single Sign-On을 구성하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single Sign-On**을 클릭합니다.

8.  **암호 기반 로그온** 모드를 선택합니다.

9.  **로그온 URL**을 입력합니다. 사용자가 로그인하기 위해 사용자 이름과 암호를 입력하는 URL입니다. URL에서 로그인 필드가 표시되는지 확인합니다.

10. 응용 프로그램에 사용자를 할당합니다.

11. 또한 사용자의 행을 선택하고 **자격 증명 업데이트**를 클릭하고 사용자를 대신하여 사용자 이름 및 암호를 입력하여 사용자를 대신하여 자격 증명을 제공할 수도 있습니다. 그렇지 않으면 사용자는 시작할 때 자격 증명을 입력하라는 메시지를 받게 됩니다.

### <a name="assign-users-to-the-application"></a>응용 프로그램에 사용자 할당

응용 프로그램에 하나 이상의 사용자를 직접 할당하려면 다음 단계를 수행합니다.

1.  [**Azure Portal**](https://portal.azure.com/)을 열고 **전역 관리자** 권한으로 로그인합니다.

2.  왼쪽 주 탐색 메뉴의 맨 위에서 **모든 서비스**를 클릭하여 **Azure Active Directory 확장**을 엽니다.

3.  필터 검색 상자에 **“Azure Active Directory**”를 입력하고 **Azure Active Directory** 항목을 선택합니다.

4.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램**을 클릭합니다.

5.  **모든 응용 프로그램**을 클릭하여 모든 응용 프로그램의 목록을 봅니다.

   * 여기에 표시하려는 응용 프로그램이 표시되지 않으면 **모든 응용 프로그램 목록**의 맨 위에서 **필터** 컨트롤을 사용하고 **표시** 옵션을 **모든 응용 프로그램**으로 설정합니다.

6.  목록에서 사용자를 할당하려는 응용 프로그램을 선택합니다.

7.  응용 프로그램이 로드되면 응용 프로그램의 왼쪽 탐색 메뉴에서 **사용자 및 그룹**을 클릭합니다.

8.  **사용자 및 그룹** 목록의 맨 위에서 **추가** 단추를 클릭하여 **할당 추가** 창을 엽니다.

9.  **할당 추가** 창에서 **사용자 및 그룹** 선택기를 클릭합니다.

10. **이름 또는 전자 메일 주소로 검색** 검색 상자에 할당하려는 사용자의 **전체 이름** 또는 **전자 메일 주소**를 입력합니다.

11. 목록의 **사용자** 위로 마우스를 이동하여 **확인란**을 표시합니다. 사용자의 프로필 사진이나 로고 옆의 확인란을 클릭하여 사용자를 **선택됨** 목록에 추가합니다.

12. **선택 사항:** **둘 이상의 사용자를 추가**하려는 경우 **이름 또는 전자 메일 주소로 검색** 검색 상자에 다른 **전체 이름** 또는 **전자 메일 주소**를 입력하고 확인란을 클릭하여 이 사용자를 **선택됨** 목록에 추가합니다.

13. 사용자 선택이 완료되면 **선택** 단추를 클릭하여 응용 프로그램에 할당되도록 사용자 및 그룹의 목록에 추가합니다.

14. **선택 사항:** **할당 추가** 창에서 **역할 선택** 선택기를 클릭하여 선택한 사용자에게 할당할 역할을 선택합니다.

15. **할당** 단추를 클릭하여 선택한 사용자에게 응용 프로그램을 할당합니다.

짧은 시간 후에 선택한 사용자는 액세스 패널에서 이러한 응용 프로그램을 시작할 수 있습니다.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>이러한 문제 해결 단계가 문제를 해결하지 않는 경우

사용할 수 있는 경우 다음 정보로 지원 티켓을 엽니다.

-   상관 관계 오류 ID

-   UPN(사용자 전자 메일 주소)

-   TenantID

-   브라우저 종류

-   오류가 발생하는 동안 표준 시간대 및 시간/기간

-   Fiddler 추적

## <a name="next-steps"></a>다음 단계
[응용 프로그램 프록시를 사용하여 앱에 Single Sign-On 제공](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

