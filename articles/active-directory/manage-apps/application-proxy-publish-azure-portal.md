---
title: Azure AD 응용 프로그램 프록시를 사용하여 앱 게시 | Microsoft Docs
description: Azure Portal에서 Azure AD 응용 프로그램 프록시를 사용하여 클라우드에 온-프레미스 응용 프로그램을 게시합니다.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 6ab0e36fc0b4bd3f04d75499b6dfe734a9f72d4d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시

Azure AD(Active Directory) 응용 프로그램 프록시를 사용하면 인터넷을 통해 액세스할 수 있는 온-프레미스 응용 프로그램을 게시하여 원격 작업자를 지원할 수 있습니다. Azure Portal을 통해 이러한 응용 프로그램을 게시하여 네트워크 외부에서 보안 원격 액세스를 제공할 수 있습니다.

이 문서에서는 응용 프로그램 프록시를 통해 온-프레미스 앱을 게시하는 방법을 단계별로 안내합니다. 이 문서를 완료하면 사용자가 원격으로 앱에 액세스할 수 있습니다. 그리고 Single Sign-On, 개인 설정 정보 및 보안 요구 사항과 같이 응용 프로그램에 대한 추가 기능을 구성할 준비가 된 것입니다.

응용 프로그램 프록시를 처음 사용하는 경우 [온-프레미스 응용 프로그램에 보안된 원격 액세스를 제공하는 방법](application-proxy.md) 문서에서 이 기능에 대해 자세히 알아보세요.


## <a name="publish-an-on-premises-app-for-remote-access"></a>원격 액세스를 위한 온-프레미스 앱 게시

다음 단계에 따라 응용 프로그램 프록시를 사용하여 앱을 게시합니다. 조직에 대한 커넥터를 아직 다운로드하여 구성하지 않은 경우 먼저 [응용 프로그램 프록시를 시작하고 커넥터를 설치](application-proxy-enable.md)한 다음 앱을 게시합니다.

> [!TIP]
> 응용 프로그램 프록시를 처음 테스트하는 경우 암호 기반 인증으로 설정된 응용 프로그램을 선택합니다. 응용 프로그램 프록시에서는 다른 유형의 인증을 지원하지만 암호 기반 앱이 가장 빠르고 쉽게 시작하고 실행할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com/)에서 관리자로 로그인합니다.
2. **Azure Active Directory** > **Enterprise 응용 프로그램** > **새로운 응용 프로그램**을 선택합니다.

  ![엔터프라이즈 응용 프로그램 추가](./media/application-proxy-publish-azure-portal/add-app.png)

3. **모두**를 선택한 다음 **온-프레미스 응용 프로그램**을 선택합니다.  

  ![응용 프로그램 직접 추가](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. 응용 프로그램에 대한 다음과 같은 정보를 제공합니다.

   - **이름**: 액세스 패널 및 Azure Portal에 표시될 응용 프로그램의 이름입니다. 

   - **내부 URL**: 개인 네트워크 내부에서 응용 프로그램에 액세스하는 데 사용하는 URL입니다. 나머지 서버는 게시되지 않은 반면 게시할 백 앤드 서버에 특정 경로를 제공할 수 있습니다. 이렇게 하면 다른 앱과 동일한 서버에 여러 사이트를 게시하고 각 사이트에 고유한 이름과 액세스 규칙을 부여할 수 있습니다.

     > [!TIP]
     > 경로를 게시하는 경우 응용 프로그램에 필요한 이미지, 스크립트 및 스타일 시트를 모두 포함하는지 확인합니다. 예를 들어 앱이 https://yourapp/app에 있고 https://yourapp/media에 있는 이미지를 사용하는 경우 경로로 https://yourapp/을 게시해야 합니다. 이 내부 URL은 사용자에게 표시되는 방문 페이지일 필요가 없습니다. 자세한 내용은 [게시된 앱에 대해 사용자 지정 홈페이지 설정](application-proxy-configure-custom-home-page.md)을 참조하세요.

   - **외부 URL**: 네트워크 외부에서 앱에 액세스하기 위해 사용자가 이동할 주소입니다. 기본 응용 프로그램 프록시 도메인을 사용하지 않으려면 [Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)을 참조하세요.
   - **사전 인증**: 응용 프로그램 프록시가 사용자에게 응용 프로그램에 대한 액세스 권한을 부여하기 전에 사용자를 확인하는 방법입니다. 

     - Azure Active Directory: 응용 프로그램 프록시는 Azure AD를 사용하여 로그인하는 사용자를 리디렉션하여 디렉터리와 응용 프로그램에 대한 사용 권한을 인증합니다. 조건부 액세스 및 Multi-Factor Authentication과 같은 Azure AD 보안 기능을 활용할 수 있도록 이 옵션을 기본값으로 유지하는 것이 좋습니다.
     - 통과: 사용자는 응용 프로그램에 액세스하기 위해 Azure Active Directory에 대해 인증할 필요가 없습니다. 백 엔드에 대한 인증 요구 사항은 여전히 설정할 수 있습니다.
   - **커넥터 그룹**: 커넥터는 응용 프로그램에 대한 원격 액세스를 처리하고, 커넥터 그룹은 지역, 네트워크 또는 용도별로 커넥터와 앱을 구성하는 데 도움을 줍니다. 아직 만든 커넥터 그룹이 없는 경우 앱이 **Default**(기본값)로 할당됩니다.

>[!NOTE]
>응용 프로그램에서 연결을 위해 websocket을 사용하는 경우 websocket 지원이 있는 커넥터 버전 1.5.612.0 이상이 있고 할당된 커넥터 그룹에서 이러한 커넥터만을 사용하는지 확인합니다.

   ![응용 프로그램 구성](./media/application-proxy-publish-azure-portal/configure-app.png)
5. 필요한 경우 추가 설정을 구성합니다. 대부분의 응용 프로그램에서는 다음과 같은 설정을 기본 상태로 유지해야 합니다. 
   - **백 엔드 응용 프로그램 시간 제한**: 응용 프로그램의 인증 및 연결 속도가 느린 경우에만 이 값을 **Long**(길게)으로 설정합니다. 
   - **헤더의 URL 변환**: 응용 프로그램이 인증 요청에서 원래 호스트 헤더를 요구하지 않는 한 이 값을 **Yes**(예)로 유지합니다.
   - **응용 프로그램 본문의 URL 변환**: 다른 온-프레미스 응용 프로그램에 HTML 링크를 하드 코드하지 않고 사용자 지정 도메인을 사용하지 않는 한 이 값을 **No**(아니요)로 유지합니다. 자세한 내용은 [응용 프로그램 프록시를 사용한 링크 변환](application-proxy-configure-hard-coded-link-translation.md)을 참조하세요.
   
   ![응용 프로그램 구성](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. **추가**를 선택합니다.


## <a name="add-a-test-user"></a>테스트 사용자 추가 

앱이 제대로 게시되었는지 테스트하려면 테스트 사용자 계정을 추가합니다. 이 계정에 이미 회사 네트워크 내부에서 앱에 액세스할 수 있는 권한이 있는지 확인합니다.

1. [빠른 시작] 블레이드로 돌아가서 **테스트할 사용자 지정**을 선택합니다.

  ![테스트할 사용자 지정](./media/application-proxy-publish-azure-portal/assign-user.png)

2. [사용자 및 그룹] 블레이드에서 **추가**를 선택합니다.

  ![사용자 또는 그룹 추가](./media/application-proxy-publish-azure-portal/add-user.png)

3. [할당 추가] 블레이드에서 **사용자 및 그룹**을 선택한 다음 추가할 계정을 선택합니다. 
4. **할당**을 선택합니다.

## <a name="test-your-published-app"></a>게시된 앱 테스트

브라우저에서 게시 단계에서 구성한 외부 URL로 이동합니다. 시작 화면이 표시되고 설정한 테스트 계정으로 로그인할 수 있습니다.

![게시된 앱 테스트](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>다음 단계
- [커넥터 다운로드](application-proxy-enable.md) 및 [커넥터 그룹 만들기](application-proxy-connector-groups.md)를 수행하여 별도의 네트워크와 위치에 응용 프로그램을 게시합니다.

- 새로 게시된 앱에 대해 [Single Sign-On을 설정](application-proxy-configure-single-sign-on-password-vaulting.md)합니다.
