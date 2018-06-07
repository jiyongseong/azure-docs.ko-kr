---
title: 응용 프로그램 액세스 패널 브라우저 확장 설치 - Azure | Microsoft Docs
description: 액세스 패널 브라우저 확장을 설치할 때 발생하는 일반적인 오류를 해결합니다.
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
ms.date: 5/4/18
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 8db0c28b0ea6b6f78ef86c773ff24f9d22fe3ecd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353877"
---
# <a name="install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장 설치

액세스 패널은 웹 기반 포털입니다. Azure AD(Azure Active Directory)에 회사 또는 학교 계정이 있는 경우 액세스 패널을 사용하면 Azure AD 관리자로부터 액세스 권한을 부여받은 클라우드 기반 응용 프로그램을 볼 수 있고 시작할 수 있습니다. 

Azure AD 버전을 사용하는 경우에는 액세스 패널을 통해 셀프 서비스 그룹 및 앱 관리 기능을 사용할 수도 있습니다. 

액세스 패널은 Azure Portal과 별개입니다. Azure 구독이 필요하지 않습니다.

## <a name="web-browser-requirements"></a>웹 브라우저 요구 사항

액세스 패널에는 최소한 JavaScript를 지원하고 CSS를 사용하도록 설정된 브라우저가 필요합니다. 액세스 패널에서 암호 기반 SSO를 통해 응용 프로그램에 로그인하려면 브라우저에 액세스 패널 확장이 설치되어 있어야 합니다. 확장은 암호 기반 SSO용으로 구성된 응용 프로그램을 선택하면 자동으로 다운로드됩니다.

암호 기반 SSO에는 다음 브라우저 중 하나를 사용할 수 있습니다.

- **Edge**: Windows 10 Anniversary Edition 이상 
- **Chrome**: Windows 7 이상 및 MacOS X 이상
- **Firefox 26.0 이상**: Windows XP SP2 이상 및 Mac OS X 10.6 이상

## <a name="install-the-access-panel-browser-extension"></a>액세스 패널 브라우저 확장 설치

액세스 패널 브라우저 확장을 설치하려면 다음을 수행합니다.

1.  지원되는 브라우저 중 하나에서 [액세스 패널](https://myapps.microsoft.com)을 열고 Azure AD 계정에 사용자로 로그인합니다.

2.  암호 기반 SSO 응용 프로그램을 선택합니다.

3.  메시지가 표시되면 **지금 설치**를 선택합니다.  
    선택한 브라우저에 해당하는 다운로드 링크로 연결됩니다. 
    
4.  **추가**를 선택합니다.

5.  메시지가 나타나면 확장을 **활성화** 또는 **허용**합니다.

6.  설치가 완료되면 브라우저를 다시 시작합니다.

7.  액세스 패널에 로그인하고 암호 기반 SSO 응용 프로그램을 시작할 수 있는지 확인합니다.

다음 사이트에서 Chrome 및 Edge에 대한 확장을 직접 다운로드할 수도 있습니다.

- [Chrome 확장](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Edge 확장](https://www.microsoft.com/store/apps/9pc9sckkzk84) 

## <a name="use-the-my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장 사용
* `https://myapps.microsoft.com` 이외의 My Apps URL을 사용하는 경우 다음을 수행하여 기본 URL을 구성합니다.
   1. 확장에 로그인되지 *않은* 상태에서 확장 아이콘을 마우스 오른쪽 단추로 클릭합니다.
   2. 메뉴에서 **My Apps URL**을 선택합니다.
   3. 기본 URL을 선택합니다.
   4. 확장 아이콘을 선택합니다.
   5. 확장에 로그인하려면 **시작하려면 로그인하세요**를 선택합니다.

* 브라우저에서 앱에 직접 로그인하려면 다음을 수행합니다.
   1. 확장이 설치되면 **시작하려면 로그인하세요**를 선택하여 확장에 로그인합니다.
   2. 로그온 URL로 앱에 로그인합니다.  
       로그온 URL은 일반적으로 로그인 양식을 표시하는 앱의 URL입니다.
      확장에서 상태를 변경하고 암호를 사용할 수 있다고 알려주어야 합니다.
   3. 로그인하려면 확장 아이콘을 선택합니다.

* 확장에서 앱을 시작하려면 다음을 수행합니다.
   1. 확장이 설치되면 **시작하려면 로그인하세요**를 선택하여 확장에 로그인합니다.
   2. 확장 아이콘을 선택하여 해당 메뉴를 엽니다.
   3. My Apps 포털에서 사용할 수 있는 앱을 검색합니다.
   4. 검색 결과 목록에서 앱을 선택합니다.  
       마지막으로 사용한 세 개의 앱이 **최근에 사용한 항목** 바로 가기 목록에 표시됩니다.
       
* 원격으로 회사 내부 URL을 사용하려면 다음을 수행합니다.
    1. 테넌트에 [응용 프로그램 프록시 구성](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-enable)
    2. 응용 프로그램 프록시를 통해 [응용 프로그램 및 URL 게시](https://docs.microsoft.com/en-us/azure/active-directory/application-proxy-publish-azure-portal)
    3. 확장을 설치하고, 시작하려면 로그인을 선택하여 확장에 로그인
    4. 이제 원격으로도 내부 회사 URL로 이동할 수 있습니다.

> [!NOTE]
> 위의 옵션은 Edge, Chrome 및 Firefox에서만 사용할 수 있습니다.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Internet Explorer에 대한 그룹 정책 설정

사용자의 컴퓨터에 Internet Explorer용 액세스 패널 확장을 원격으로 설치할 수 있는 그룹 정책을 설정할 수 있습니다.

그룹 정책을 설정하기 전에 다음을 확인합니다.

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)를 설정하고, 사용자 컴퓨터를 도메인에 가입시킨 상태여야 합니다.

-   GPO(그룹 정책 개체)를 편집하려면 *설정 편집* 권한이 있어야 합니다. 기본적으로 이 권한은 도메인 관리자, 엔터프라이즈 관리자 및 그룹 정책 작성자 소유자 보안 그룹의 멤버에게 부여됩니다.

그룹 정책을 구성하고 사용자에게 배포하는 방법에 대한 단계별 지침은 [그룹 정책을 사용하여 Internet Explorer용 액세스 패널 확장 배포](active-directory-saas-ie-group-policy.md)를 참조하세요.

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Internet Explorer에서 액세스 패널 확장 문제 해결

Internet Explorer용 확장 구성에 대한 정보 및 진단 도구에 대한 액세스는 [Internet Explorer의 액세스 패널 확장 문제 해결](active-directory-saas-ie-troubleshooting.md)을 참조하세요.

> [!NOTE]
> Internet Explorer는 지원이 제한되며, 새 소프트웨어 업데이트가 더 이상 수신되지 않습니다. Edge가 권장되는 브라우저입니다.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>위 단계를 수행해도 문제가 해결되지 않으면

가능한 경우 다음 정보가 포함된 지원 티켓을 엽니다.

-   상관 관계 오류 ID
-   UPN(사용자 전자 메일 주소)
-   TenantID
-   브라우저 종류
-   오류가 발생한 표준 시간대 및 시간 또는 시간 범위
-   Fiddler 추적

## <a name="next-steps"></a>다음 단계
[Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](manage-apps/what-is-single-sign-on.md)
