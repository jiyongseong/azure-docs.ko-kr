---
title: Azure Active Directory에서 액세스 패널이란? | Microsoft Docs
description: 다양한 액세스 패널(웹 브라우저, Android 앱, iPhone 및 iPad 앱)을 사용하여 SaaS 앱에 액세스하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/18
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 982e2cb6780e5285dc5849d655efdf830f76cf13
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347502"
---
# <a name="what-is-the-access-panel"></a>액세스 패널이란?

액세스 패널은 웹 기반 포털입니다. Azure AD(Azure Active Directory)에 회사 또는 학교 계정이 있는 경우 액세스 패널을 사용하면 Azure AD 관리자로부터 액세스 권한을 부여받은 클라우드 기반 응용 프로그램을 볼 수 있고 시작할 수 있습니다. 또한 액세스 패널을 통해 셀프 서비스 그룹 및 앱 관리 기능을 사용할 수도 있습니다.

액세스 패널은 Azure Portal과 별개입니다. Azure 구독이 필요하지 않습니다.

![액세스 패널][1] 액세스 패널을 사용하면 일부 프로필 설정을 편집할 수 있고 다음 작업을 수행할 수 있습니다.

- 회사 또는 학교 계정과 관련된 암호를 변경합니다.

- 암호 다시 설정을 편집합니다.

- 다단계 인증과 관련된 연락처 및 기본 설정을 편집합니다 (관리자가 사용해야 하는 계정의 경우).

- 사용자 ID, 대체 이메일, 휴대폰과 사무실 전화 번호 및 장치와 같은 계정 세부 정보를 봅니다.

- Azure AD 관리자로부터 액세스 권한을 부여받은 클라우드 기반 응용 프로그램을 보거나 시작합니다. 

- 그룹을 셀프 관리합니다. 관리자는 Azure AD에서 보안 그룹을 만들고 관리할 수 있으며 보안 그룹 멤버 자격을 요청할 수 있습니다. 자세한 내용은 [Azure AD의 사용자를 위한 셀프 서비스 그룹 관리](active-directory-accessmanagement-self-service-group-management.md)와 [그룹 관리](active-directory-manage-groups.md)를 참조하세요.




## <a name="access-the-access-panel"></a>액세스 패널에 액세스

액세스 패널은 `http://myapps.microsoft.com`으로 이동하여 액세스할 수 있습니다.

로그인 페이지에서 사용자 지정 브랜딩을 구성한 경우 조직의 도메인을 URL에 추가하여(예: `http://myapps.microsoft.com/<your domain>.com`) 브랜딩을 로드할 수 있습니다.

다음과 같이 Azure Portal에 구성되어 있는 활성 또는 확인된 도메인 이름을 사용할 수 있습니다. ![Wingtip Toys 도메인 이름][2]  

Azure AD와 통합된 응용 프로그램에 로그인하는 모든 사용자에게 URL을 배포합니다.

## <a name="authentication"></a>인증

액세스 패널에 도달하려면 Azure AD에서 회사 또는 학교 계정을 통해 인증을 받아야 합니다. Azure AD에서 직접 인증을 받을 수 있습니다. 또는 조직에서 AD FS(Active Directory Federation Services) 또는 다른 기술을 사용하여 페더레이션을 구성한 경우 Windows Server Active Directory에서 인증을 받을 수도 있습니다.

Azure 또는 Office 365에 대한 구독이 있고 Azure Portal 또는 Office 365 응용 프로그램을 사용하고 있으면, 다시 로그인하지 않고 응용 프로그램의 목록을 볼 수 있습니다. 인증되지 않은 경우 Azure AD의 계정에 대한 사용자 이름과 암호를 사용하여 로그인하라는 메시지가 표시됩니다. 조직에서 페더레이션을 구성한 경우 사용자 이름만 입력하면 됩니다.

인증되면 관리자에 의해 디렉터리와 통합된 응용 프로그램과 상호 작용할 수 있습니다. 응용 프로그램을 Azure AD와 통합하는 방법을 보려면 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="web-browser-requirements"></a>웹 브라우저 요구 사항

액세스 패널에는 최소한 JavaScript를 지원하고 CSS를 사용하도록 설정된 브라우저가 필요합니다. 암호 기반 SSO(Single Sign-On)를 통해 응용 프로그램에 로그인하려면 브라우저에 액세스 패널 확장이 설치되어 있어야 합니다. 암호 기반 SSO에 구성된 응용 프로그램을 선택할 때 확장이 자동으로 다운로드됩니다.

설치 관리자는 아키텍처마다 다릅니다. 다운로드 링크를 클릭하면 현재 실행 중인 OS 아키텍처에 대한 설치 관리자만 가져옵니다. 응용 프로그램 배포 관리자인 경우 64비트 및 32비트 장치에서 다운로드 링크를 방문하여 두 설치 관리자를 모두 가져와야 합니다.


액세스 패널 확장은 현재 다음과 같은 경우 사용할 수 있습니다.
- **Edge**: Windows 10 Anniversary Edition 이상 
- **Chrome**: Windows 7 이상 및 MacOS X 이상
- **Firefox 26.0 이상**: Windows XP SP2 이상 및 Mac OS X 10.6 이상
- **Internet Explorer 11**: Windows 7 이상(제한적 지원)

## <a name="my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장
암호 기반 Single Sign-On에 로그인하려면 확장을 반드시 사용해야 합니다. 확장이 설치된 후에는 확장에 로그인하고 **시작하려면 로그인하세요**를 선택하여 추가 기능을 사용하도록 설정할 수 있습니다. 

- 앱의 **로그온 URL**을 사용하면 앱에 바로 로그인할 수 있습니다. 앱의 URL을 사용하면 확장에서 작업을 검색하여 확장을 통해 로그인할 수 있는 옵션을 제공합니다.
- 확장의 *빠른 검색* 기능을 사용하면 액세스 패널에서 어떤 앱이든 시작할 수 있습니다. 
- 확장의 **최근에 사용됨** 섹션에는 최근에 시작한 세 가지 응용 프로그램이 표시됩니다.
- 회사 내부 URL은 [응용 프로그램 프록시](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-get-started)를 통해 원격으로 사용할 수 있습니다.

> [!NOTE]
> 추가 기능은 Edge, Chrome 및 Firefox에서만 사용할 수 있습니다.
>
다음 사이트에서 확장을 직접 다운로드할 수 있습니다.
- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

`https://myapps.microsoft.com` 이외의 My Apps URL을 사용하는 경우 다음을 수행하여 기본 URL을 구성합니다.
1. 확장에 로그인되지 *않은* 상태에서 확장 아이콘을 마우스 오른쪽 단추로 클릭합니다.
2. 메뉴에서 **My Apps URL**을 선택합니다.
3. 기본 URL을 선택합니다.
4. 확장 아이콘을 선택합니다.
5. **시작하려면 로그인하세요**를 선택합니다.

원격으로 확장을 사용하여 회사 내부 URL을 사용하려면 다음을 수행합니다.
1. 테넌트에 [응용 프로그램 프록시를 구성](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-enable)합니다.
2. 응용 프로그램 프록시를 통해 [응용 프로그램 및 URL을 게시](https://docs.microsoft.com/en-us/azure/active-directory/application-proxy-publish-azure-portal)합니다.
3. 확장을 설치하고, 시작하려면 로그인을 선택하여 확장에 로그인합니다.
4. 이제는 내부 회사 URL을 원격으로도 검색할 수 있습니다.

## <a name="mobile-app-support"></a>모바일 앱 지원

Azure Active Directory 팀은 My Apps 모바일 앱을 게시합니다. 앱을 설치하면 iOS 및 Android 장치의 암호 기반 SSO 응용 프로그램에 로그인할 수 있습니다.

> [!NOTE]
> 플러그 인이나 모바일 앱 없이 모든 장치의 거의 모든 웹 브라우저(Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 및 기타 70개 이상 포함)에서 Azure AD를 사용하여 페더레이션을 지원하는 응용 프로그램에 로그인할 수 있습니다. 모바일 장치에서 사용하기 위해, 다른 [액세스 패널 환경](https://myapps.microsoft.com/)에도 My Apps 모바일 앱이 필요하지 않습니다.
>
>

### <a name="my-apps-for-android"></a>Android 용 My Apps

Android용 My Apps는 Android 버전 4.1 이상을 실행하는 모든 Android 장치에서 지원됩니다.  

[Google Play 스토어](https://play.google.com/store/apps/details?id=com.microsoft.myapps)에 제공됩니다.

![Android 용 My Apps][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>iPhone 및 iPad 용 My Apps

iOS용 My Apps는 iOS 버전 7 이상을 실행하는 iPhone 또는 iPad에서 지원됩니다.  

[Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)에 제공됩니다.

![iOS용 My Apps][4]    


## <a name="managed-browser-for-my-apps"></a>My Apps에 대한 관리되는 브라우저

My Apps는 Intune Managed Browser에도 통합되어 있습니다. iOS 및 Android 장치용 Intune Managed Browser는 모바일 장치의 데이터가 안전하게 유지되도록 하는 데 핵심적인 역할을 수행합니다. 브라우저를 사용하면 회사 정보가 포함될 수도 있는 웹 페이지를 안전하게 보고 탐색할 수 있으며 안전한 웹 브라우징 환경을 제공하는 데 도움이 됩니다.  

Managed Browser 홈 페이지와 사용자의 책갈피에서 My Apps에 빠르게 액세스할 수 있기 때문에 액세스하려는 응용 프로그램에 도달하기 위해 필요한 클릭 수가 줄어듭니다.

Intune Managed Browser는 [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) 및 [Google Play 스토어](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en)에 제공됩니다.

![My Apps에 대한 관리되는 브라우저][5]    


## <a name="tips-for-testing-the-user-experience"></a>최종 사용자 환경 테스트를 위한 팁

Azure 관리자이고 디렉터리에 있는 계정을 사용하여 Azure Portal에 로그인하면 액세스 패널에 현재 계정으로 자동 로그인됩니다. 이 보기에는 내게 할당된 모든 응용 프로그램이 표시됩니다.

*다른* 사용자 계정으로 테스트하려면 다음을 수행합니다.

1. Azure Portal 또는 액세스 패널의 오른쪽 위에서 **로그아웃**을 선택합니다. 
2. [액세스 패널](http://myapps.microsoft.com)로 이동합니다.
3. 로그인 페이지에서 테스트할 디렉터리의 계정에 대한 사용자 이름과 암호를 입력합니다.


## <a name="starting-applications"></a>응용 프로그램 시작

이 섹션에서는 액세스 패널에 표시될 수 있는 몇 가지 유형의 응용 프로그램을 설명합니다.

### <a name="office-365-applications"></a>Office 365 응용 프로그램

조직에서 Office 365 응용 프로그램을 사용하고 있고 해당 라이선스가 있는 경우 Office 365 응용 프로그램이 액세스 패널에 표시됩니다.

Office 365 응용 프로그램에 대한 응용 프로그램 타일을 선택하면 해당 응용 프로그램으로 리디렉션되고 자동으로 로그인됩니다.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>페더레이션 기반 SSO로 구성된 Microsoft 및 타사 응용 프로그램

관리자는 Azure Portal의 Active Directory 섹션에서 **Azure AD Single Sign-On**으로 설정된 SSO 모드로 응용 프로그램을 추가할 수 있습니다. 관리자가 응용 프로그램에 대한 액세스를 명시적으로 부여한 경우에만 응용 프로그램을 볼 수 있습니다.

응용 프로그램의 타일을 선택하면 리디렉션되고 자동으로 로그인됩니다.

### <a name="password-based-sso-without-identity-provisioning"></a>ID 프로비전 없는 암호 기반 SSO

관리자는 Azure Portal의 Active Directory 섹션에서 **암호 기반 Single Sign-On**으로 설정된 SSO 모드로 응용 프로그램을 추가할 수 있습니다. 디렉터리의 모든 사용자는 이 모드에서 구성된 모든 응용 프로그램을 볼 수 있습니다.

응용 프로그램 타일을 처음으로 선택하면 Internet Explorer 또는 Chrome용 암호 SSO 플러그인을 설치하라는 메시지가 표시됩니다. 설치 시 웹 브라우저를 다시 시작해야 할 수도 있습니다. 액세스 패널로 돌아와서 응용 프로그램 타일을 다시 선택하면 응용 프로그램에 대한 사용자 이름과 및 암호를 묻는 메시지가 표시됩니다. 사용자 이름과 암호를 입력하면 자격 증명이 Azure AD의 해당 계정에 안전하게 저장되고 연결됩니다.

다음에 응용 프로그램 타일을 선택하면 해당 응용 프로그램에 자동으로 로그인됩니다.  

자격 증명을 다시 입력하거나 암호 SSO 플러그 인을 설치할 필요가 없습니다.

자격 증명이 대상 타사 응용 프로그램에서 변경된 경우 Azure AD에 저장된 자격 증명도 업데이트해야 합니다. 

자격 증명을 업데이트하려면 다음을 수행합니다.

1. 응용 프로그램 타일에 있는 아이콘을 선택합니다.
2. **자격 증명 업데이트**를 선택하여 응용 프로그램에 대한 사용자 이름 및 암호를 다시 입력합니다.


### <a name="password-based-sso-with-identity-provisioning"></a>ID 프로비전을 사용한 암호 기반 SSO

관리자는 Azure Portal의 Active Directory 섹션에서 ID 프로비전과 함께 SSO 모드가 **암호 기반 Single Sign-On**으로 설정된 응용 프로그램을 추가할 수 있습니다.

응용 프로그램 타일을 처음으로 선택하면 Internet Explorer 또는 Chrome용 암호 SSO 플러그인을 설치하라는 메시지가 표시됩니다. 설치 시 웹 브라우저를 다시 시작해야 할 수도 있습니다.  

액세스 패널로 돌아와서 응용 프로그램 타일을 다시 선택하면 응용 프로그램에 자동으로 로그인됩니다.

일부 응용 프로그램에서는 처음 로그인할 때 암호를 변경해야 할 수도 있습니다. 자격 증명이 대상 타사 응용 프로그램에서 변경된 경우 Azure AD에 저장된 자격 증명도 업데이트해야 합니다. 

자격 증명을 업데이트하려면 다음을 수행합니다.

1. 응용 프로그램 타일에 있는 아이콘을 선택합니다.
2. **자격 증명 업데이트**를 선택하여 응용 프로그램에 대한 사용자 이름 및 암호를 다시 입력합니다.


### <a name="application-with-existing-sso-solutions"></a>기존 SSO 솔루션을 사용한 응용 프로그램

응용 프로그램에 SSO를 구성하기 위해 Azure Portal에서는 기존 Single Sign-On이라는 세 번째 옵션을 제공합니다. 이 옵션을 사용하면 관리자가 응용 프로그램에 대한 링크를 만들어 선택한 사용자의 액세스 패널에 배치할 수 있습니다.

예를 들어 응용 프로그램이 AD FS 2.0을 사용하여 사용자를 인증하도록 구성되어 있는 경우 관리자는 기존 Single Sign-On 옵션을 사용하여 액세스 패널에 해당 응용 프로그램에 대한 링크를 만들 수 있습니다. 이 링크에 액세스하면 AD FS 2.0 또는 응용 프로그램에서 제공하는 기존 SSO 솔루션을 통해 인증됩니다.


## <a name="next-steps"></a>다음 단계

- 응용 프로그램 관리와 관련된 모든 항목의 목록을 보려면 [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)를 참조하세요.
 
- SaaS 앱을 Azure AD와 통합하는 방법을 알아보려면 [SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)을 참조하세요.
 
- Azure AD를 사용하여 앱 관리에 대한 자세한 내용은 [Azure Active Directory로 SSO(Single Sign-On) 및 앱 액세스 관리 소개](manage-apps/what-is-single-sign-on.md)를 참조하세요.
 
- 사용자 프로비저닝에 대한 자세한 내용은 [SaaS 응용 프로그램에 자동화된 사용자 프로비저닝 및 프로비저닝 해제](active-directory-saas-app-provisioning.md)를 참조하세요.

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
