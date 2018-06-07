---
title: Azure AD 응용 프로그램 프록시용 PingAccess를 통한 헤더 기반 인증 | Microsoft Docs
description: PingAccess 및 앱 프록시를 사용하여 응용 프로그램을 게시하여 헤더 기반 인증을 지원합니다.
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
ms.date: 10/11/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: f00c22a5a7bde69136c674c1362e3a9d65dd7934
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>응용 프로그램 프록시 및 PingAccess를 사용하여 Single Sign-On에 대한 헤더 기반 인증

Azure Active Directory 응용 프로그램 프록시 및 PingAccess는 Azure Active Directory 고객에게 더 많은 응용 프로그램에 대한 액세스를 제공하도록 파트너 관계를 맺고 있습니다. PingAccess는 [기존 응용 프로그램 프록시 제품](application-proxy.md)을 확장하여 인증에 헤더를 사용하는 응용 프로그램에 대한 Single Sign-On 액세스를 포함하고 있습니다.

## <a name="what-is-pingaccess-for-azure-ad"></a>Azure AD용 PingAccess는 무엇입니까?

Azure Active Directory용 PingAccess는 인증에 헤더를 사용하는 응용 프로그램에 대한 액세스 및 Single Sign-On을 사용자에게 부여할 수 있도록 하는 PingAccess 제품입니다. 응용 프로그램 프록시는 Azure AD를 사용하여 액세스를 인증한 다음 커넥터 서비스를 통해 트래픽을 전달하여 다른 앱과 같이 이러한 앱을 처리합니다. PingAccess는 응용 프로그램에서 읽을 수 있는 형식으로 인증을 받도록 앱 앞에 위치하고 Azure AD의 액세스 토큰을 헤더로 변환합니다.

사용자는 회사 앱을 사용하기 위해 로그인할 때 다른 점을 알아차리지 못합니다. 여전히 어디에서든지 모든 장치에서 작업할 수 있습니다. 

응용 프로그램 프록시 커넥터는 해당 인증 유형에 관계 없이 모든 앱에 원격 트래픽을 보내므로 계속해서 자동으로 부하 균형을 유지합니다.

## <a name="how-do-i-get-access"></a>액세스 권한은 어떻게 얻을 수 있나요?

이 시나리오는 Azure Active Directory 및 PingAccess 간의 파트너 관계를 통해 제공되므로 두 서비스에 대한 라이선스가 필요합니다. 그러나 Azure Active Directory Premium 구독에는 최대 20개의 응용 프로그램을 보장하는 기본 PingAccess 라이선스가 포함되어 있습니다. 헤더 기반 응용 프로그램을 20개 이상 게시해야 하는 경우 PingAccess에서 라이선스를 추가로 구입할 수 있습니다. 

자세한 내용은 [Azure Active Directory 버전](../active-directory-whatis.md)을 참조하세요.

## <a name="publish-your-application-in-azure"></a>Azure에 응용 프로그램 게시

이 문서는 처음으로 이 시나리오를 사용하여 앱을 게시하는 사람들을 위한 것입니다. 게시 단계 외에도 응용 프로그램과 PingAccess를 시작하는 방법을 안내합니다. 두 서비스를 이미 구성했지만 게시 단계에서 리프레셔를 원하는 경우 커넥터 설치를 건너뛰고 [응용 프로그램 프록시를 사용하여 Azure AD에 앱 추가](#add-your-app-to-Azure-AD-with-Application-Proxy)로 이동할 수 있습니다.

>[!NOTE]
>이 시나리오는 Azure AD와 PingAccess 간의 파트너 관계이므로 일부 지침은 Ping ID 사이트에 존재합니다.

### <a name="install-an-application-proxy-connector"></a>응용 프로그램 프록시 커넥터 설치

이미 응용 프로그램 프록시를 사용하도록 설정되어 있고 커넥터가 설치되어 있는 경우 이 섹션을 건너뛰고 [응용 프로그램 프록시를 사용하여 Azure AD에 앱 추가](#add-your-app-to-azure-ad-with-application-proxy)로 이동할 수 있습니다.

응용 프로그램 프록시 커넥터는 원격 직원의 트래픽을 게시된 앱으로 전달하는 Windows Server 서비스입니다. 자세한 설치 지침은 [Azure Portal에서 응용 프로그램 프록시 사용](application-proxy-enable.md)을 참조하세요.

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **응용 프로그램 프록시**를 선택합니다.
3. **커넥터 다운로드**를 선택하여 응용 프로그램 프록시 커넥터 다운로드를 시작합니다. 설치 지침을 따릅니다.

   ![응용 프로그램 프록시를 사용하도록 설정하고 커넥터 다운로드](./media/application-proxy-configure-single-sign-on-with-ping-access/install-connector.png)

4. 커넥터를 다운로드하면 디렉터리에 대해 응용 프로그램 프록시가 자동으로 활성화되지만 그렇지 않은 경우 **응용 프로그램 프록시 사용**을 선택할 수 있습니다.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>응용 프로그램 프록시를 사용하여 Azure AD에 앱 추가

Azure Portal에서 수행해야 하는 두 가지 작업이 있습니다. 먼저 응용 프로그램 프록시를 사용하여 응용 프로그램을 게시해야 합니다. 그런 다음 PingAccess 단계에서 사용할 수 있는 해당 앱에 대한 정보를 수집해야 합니다.

앱을 게시하려면 다음 단계를 수행합니다. 1 ~ 8 단계에 대한 자세한 연습은 [Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시](application-proxy-publish-azure-portal.md)를 참조하세요.

1. 마지막 섹션에 있지 않았던 경우 전역 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **Enterprise 응용 프로그램**을 선택합니다.
3. 블레이드의 위쪽에서 **추가**를 선택합니다.
4. **온-프레미스 응용 프로그램**을 선택합니다.
5. 새 앱에 대한 정보로 필수 필드를 작성합니다. 설정에 대해 다음 지침을 사용합니다.
   - **내부 URL**: 회사 네트워크에 있을 때 일반적으로 앱의 로그인 페이지로 안내하는 URL을 제공합니다. 이 시나리오의 경우 커넥터에서 PingAccess 프록시를 앱의 기본 페이지로 처리해야 합니다. `https://<host name of your PA server>:<port>` 형식을 사용합니다. 포트는 기본적으로 3000이지만 PingAccess에서 구성할 수 있습니다.

    > [!WARNING]
    > 이 형식의 SSO의 경우 내부 URL은 https를 사용해야 하고 http를 사용할 수 없습니다.

   - **사전 인증 방법**: Azure Active Directory
   - **헤더에서 URL 변환**: 아니요

   >[!NOTE]
   >첫 번째 응용 프로그램인 경우 3000 포트를 사용하여 시작하고 PingAccess 구성을 변경하면 다시 돌아와서 이 설정을 업데이트합니다. 두 번째 이상의 앱인 경우 PingAccess에서 구성한 수신기와 일치해야 합니다. [PingAccess의 수신기](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html)에 대해 자세히 알아봅니다.

6. 블레이드의 아래쪽에서 **추가**를 선택합니다. 응용 프로그램이 추가되고 빠른 시작 메뉴가 열립니다.
7. 빠른 시작 메뉴에서 **테스트할 사용자 지정**을 선택하고 응용 프로그램에 하나 이상의 사용자를 추가합니다. 이 테스트 계정에 온-프레미스 응용 프로그램에 대한 액세스 권한이 있는지 확인합니다.
8. **할당**을 선택하여 테스트 사용자 할당을 저장합니다.
9. 앱 관리 블레이드에서 **Single Sign-On**을 선택합니다.
10. 드롭다운 메뉴에서 **헤더 기반 로그온**을 선택합니다. **저장**을 선택합니다.

   >[!TIP]
   >헤더 기반 Single Sign-On을 처음 사용하는 경우 PingAccess를 설치해야 합니다. Azure 구독이 PingAccess 설치와 자동으로 연결되도록 하려면 이 Single Sign-On 페이지의 링크를 사용하여 PingAccess를 다운로드합니다. 지금 다운로드 사이트를 열거나 나중에 이 페이지로 다시 돌아올 수 있습니다. 

   ![헤더 기반 로그온 선택](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.PNG)

11. Enterprise 응용 프로그램 블레이드를 닫거나 왼쪽으로 스크롤하여 Azure Active Directory 메뉴로 돌아갑니다.
12. **앱 등록**을 선택합니다.

   ![앱 등록 선택](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)

13. 방금 추가한 앱을 선택한 다음 **회신 URL**을 선택합니다.

   ![회신 URL 선택](./media/application-proxy-configure-single-sign-on-with-ping-access/reply-urls.png)

14. 5단계에서 앱에 할당한 외부 URL이 회신 URL 목록에 있는지 확인합니다. 그렇지 않은 경우 지금 추가합니다.
15. 앱 설정 블레이드에서 **필요한 권한**을 선택합니다.

  ![필요한 권한 선택](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

16. **추가**를 선택합니다. API의 경우 **Windows Azure Active Directory**를 선택한 다음 **선택**을 선택합니다. 사용 권한의 경우 **모든 응용 프로그램 읽기 및 작성**을 선택한 다음 **로그인 및 사용자 프로필 읽기**, **선택** 및 **완료**를 차례로 선택합니다.  

  ![권한 선택](./media/application-proxy-configure-single-sign-on-with-ping-access/select-permissions.png)

17. 권한을 부여한 후 사용 권한 화면을 닫습니다. 
![권한 부여](./media/application-proxy-configure-single-sign-on-with-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>PingAccess 단계에 대한 정보 수집

1. 앱 설정 블레이드에서 **속성**을 선택합니다. 

  ![속성 선택](./media/application-proxy-configure-single-sign-on-with-ping-access/properties.png)

2. **응용 프로그램 ID** 값을 저장합니다. 이 값은 PingAccess를 구성할 때 클라이언트 ID에 대해 사용됩니다.
3. 앱 설정 블레이드에서 **키**를 선택합니다.

  ![키 선택](./media/application-proxy-configure-single-sign-on-with-ping-access/Keys.png)

4. 키 설명을 입력하고 드롭다운 메뉴에서 만료 날짜를 선택하여 키를 만듭니다.
5. **저장**을 선택합니다. **값** 필드에 GUID가 나타납니다.

  이 창을 닫은 후 이 값을 다시 볼 수 없으므로 지금 저장합니다.

  ![새 키 만들기](./media/application-proxy-configure-single-sign-on-with-ping-access/create-keys.png)

6. 앱 등록 블레이드를 닫거나 왼쪽으로 스크롤하여 Azure Active Directory 메뉴로 돌아갑니다.
7. **속성**을 선택합니다.
8. **디렉터리 ID** GUID를 저장합니다.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>선택 사항 - 사용자 지정 필드를 보내도록 GraphAPI 업데이트

Azure AD에서 인증에 대해 전송하는 보안 토큰의 목록은 [Azure AD 토큰 참조](./../develop/active-directory-token-and-claims.md)를 참조하세요. 다른 토큰을 전송하는 사용자 지정 클레임이 필요한 경우 Graph 탐색기 또는 Azure Portal에서 응용 프로그램의 매니페스트를 사용하여 앱 필드 *acceptMappedClaims*를 **True**로 설정합니다.    

이 예에서는 Graph Explorer를 사용합니다.

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
이 예제는 [Azure Portal](https://portal.azure.com)을 사용하여 *acceptedMappedClaims* 필드를 업데이트합니다.
1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **앱 등록**을 선택합니다.
3. 응용 프로그램 > **매니페스트**를 선택합니다.
4. **편집**을 선택하고, *acceptedMappedClaims* 필드를 검색하고, 값을 **true**로 변경합니다.
![앱 매니페스트](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-ping-access-manifest.PNG)
1. **저장**을 선택합니다.

>[!NOTE]
>사용자 지정 클레임을 사용하려면 사용자 지정 정책도 정의하고 응용 프로그램에 할당해야 합니다.  이 정책은 필요한 모든 사용자 지정 특성을 포함해야 합니다.
>
>정책 정의 및 할당은 PowerShell, Azure AD Graph 탐색기 또는 MS Graph를 통해 수행할 수 있습니다.  이 작업을 PowerShell에서 수행하는 경우 먼저 `New-AzureADPolicy `를 사용한 후 `Set-AzureADServicePrincipalPolicy`를 사용하여 응용 프로그램에 할당해야 할 수 있습니다.  자세한 내용은 [Azure AD 정책 설명서](../active-directory-claims-mapping.md#claims-mapping-policy-assignment)를 참조하세요.

### <a name="optional---use-a-custom-claim"></a>선택 사항 - 사용자 지정 클레임 사용
응용 프로그램에서 사용자 지정 클레임을 사용하도록 하고 추가 필드를 포함하려면 [사용자 지정 클레임 매핑 정책을 만들고 응용 프로그램에 할당](../active-directory-claims-mapping.md#claims-mapping-policy-assignment)해야 합니다.

## <a name="download-pingaccess-and-configure-your-app"></a>PingAccess 다운로드 및 앱 구성

이제 Azure Active Directory 설정 단계를 모두 완료했으므로 PingAccess 구성으로 이동할 수 있습니다. 

이 시나리오의 PingAccess 부분에 대한 자세한 단계는 Ping ID 문서, [Azure AD용 PingAccess 구성](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)에서 계속됩니다.

이러한 단계는 PingAccess 계정이 아직 없는 경우 PingAccess 계정을 얻고, PingAccess 서버를 설치하고, Azure Portal에서 복사한 디렉터리 ID로 Azure AD OIDC 공급자 연결을 만드는 과정을 안내합니다. 그런 다음 응용 프로그램 ID 및 키 값을 사용하여 PingAccess에서 웹 세션을 만듭니다. 그런 다음 ID 매핑을 설정하고 가상 호스트, 사이트 및 응용 프로그램을 만들 수 있습니다.

### <a name="test-your-app"></a>앱 테스트

이러한 모든 단계를 완료하면 앱이 준비되고 실행되어야 합니다. 테스트하려면 브라우저를 열고 Azure에 앱을 게시할 때 만든 외부 URL로 이동합니다. 앱에 할당한 테스트 계정으로 로그인합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD에 대한 PingAccess 구성(영문)](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Azure AD 응용 프로그램 프록시에서 Single Sign-On을 제공하는 방법](application-proxy-single-sign-on.md)
- [응용 프로그램 프록시 문제 해결](../active-directory-application-proxy-troubleshoot.md)
