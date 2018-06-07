---
title: Azure MFA 서버 모바일 앱 웹 서비스 | Microsoft Docs
description: Microsoft Authenticator 앱을 사용하여 사용자에게 푸시 알림을 보내도록 MFA 서버를 구성합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: f6b50554e00e247d564f2be5bf579977f3bea9cf
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버를 사용하여 모바일 앱 인증 활성화

Microsoft Authenticator 앱은 추가적인 대역외 확인 옵션을 제공합니다. 로그인하는 동안 사용자에게 자동으로 전화가 걸리거나 SMS가 전송되는 대신, Azure Multi-Factor Authentication에서 사용자의 스마트폰이나 태블릿에 있는 Microsoft Authenticator 앱에 푸시 알림을 보냅니다. 사용자는 앱에서 **확인**을 탭(또는 PIN을 입력하고 "인증"을 탭)하여 로그인을 완료합니다.

전화 수신을 신뢰할 수 없는 경우 2단계 인증에 모바일 앱을 사용하는 것이 좋습니다. OATH 토큰 생성기로 앱을 사용하는 경우 네트워크 또는 인터넷 연결이 필요하지 않습니다.

사용자 환경에 따라 Azure Multi-factor Authentication 서버와 동일한 서버 또는 다른 인터넷 연결 서버에서 모바일 앱 웹 서비스를 배포하는 것이 좋습니다.

> [!IMPORTANT]
> MFA 서버 버전 8.0 이상을 설치한 경우 아래 단계의 대부분은 필요하지 않습니다. 모바일 앱 인증은 [모바일 앱 구성](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server)의 단계에 따라 설정할 수 있습니다.

## <a name="requirements"></a>요구 사항

Microsoft Authenticator 앱을 사용하려면 앱이 모바일 앱 웹 서비스와 성공적으로 통신할 수 있도록 하기 위해 다음 사항이 필요합니다.

* Azure Multi-Factor Authentication 서버 v6.x 이상
* Microsoft® [IIS(인터넷 정보 서비스) 7.x 이상](http://www.iis.net/)이 실행되는 인터넷 연결 웹 서버에 모바일 앱 웹 서비스 설치
* ASP.NET v4.0.30319가 설치, 등록되고 허용됨으로 설정
* 필요한 역할 서비스에는 ASP.NET 및 IIS 6 메타베이스 호환성이 포함됩니다.
* 모바일 앱 웹 서비스는 공용 URL을 통해 액세스할 수 있음
* 모바일 앱 웹 서비스는 SSL 인증서로 보호됩니다.
* Azure Multi-Factor Authentication 웹 서비스 SDK를 **Azure Multi-Factor Authentication 서버와 동일한 서버**의 IIS 7.x 이상에 설치
* Azure Multi-Factor Authentication 웹 서비스 SDK는 SSL 인증서로 보호됩니다.
* 모바일 웹 앱 서비스는 SSL을 통해 Azure Multi-Factor Authentication 웹 서비스 SDK에 연결할 수 있음
* 모바일 앱 웹 서비스는 "PhoneFactor Admins" 보안 그룹의 구성원인 서비스 계정의 자격 증명을 사용하여 Azure MFA 웹 서비스 SDK에 인증할 수 있습니다. 이 서비스 계정 및 그룹은 Azure Multi-Factor Authentication 서버가 도메인 연결된 서버에 있는 경우 Active Directory에 존재합니다. 도메인에 연결되지 않은 경우에는 이 서비스 계정 및 그룹이 Azure Multi-Factor Authentication 서버에 로컬로 존재합니다.

## <a name="install-the-mobile-app-web-service"></a>모바일 앱 웹 서비스 설치

모바일 앱 웹 서비스를 설치하기 전에 다음 세부 사항을 고려해야 합니다.

* v8.0 이상에서는 모바일 앱 웹 서비스를 설치할 필요가 없습니다. [모바일 앱 구성](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server)의 단계만 완료하십시오.
* "PhoneFactor Admins" 그룹의 일부인 서비스 계정이 있어야 합니다. 이 계정은 사용자 포털 설치에 사용되는 것과 동일할 수 있습니다.
* 인터넷 연결 웹 서버에서 웹 브라우저를 열고 web.config 파일에 입력된 웹 서비스 SDK의 URL로 이동하는 것이 좋습니다. 브라우저가 웹 서비스로 성공적으로 이동될 수 있으면 자격 증명을 묻는 메시지가 표시됩니다. web.config 파일에 입력된 사용자 이름 및 암호를 파일에 표시된 그대로 입력합니다. 인증서 경고 또는 오류가 표시되지 않는지 확인합니다.
* 역방향 프록시 또는 방화벽이 모바일 앱 웹 서비스 웹 서버를 차단하고 있으며 SSL 오프로드를 수행하는 경우 모바일 앱 웹 서비스 web.config 파일을 편집하여 모바일 앱 웹 서비스가 https 대신 http를 사용할 수 있습니다. 모바일 앱에서 방화벽/역방향 프록시에 SSL은 여전히 필요합니다. 다음 키를 \<appSettings\> 섹션에 추가합니다.

        <add key="SSL_REQUIRED" value="false"/>

### <a name="install-the-web-service-sdk"></a>웹 서비스 SDK 설치

두 시나리오에서 Azure Multi-Factor Authentication 웹 서비스 SDK가 Azure MFA(Multi-Factor Authentication) 서버에 아직 설치되어 있지 **않으면** 다음 단계를 완료합니다.

1. Multi-Factor Authentication 서버 콘솔을 엽니다.
2. **웹 서비스 SDK**로 이동하고 **웹 서비스 SDK 설치**를 선택합니다.
3. 어떤 이유로든 변경해야 하는 경우가 아니면 기본값을 사용하여 설치를 완료합니다.
4. IIS에서 사이트에 SSL 인증서를 바인딩합니다.

IIS 서버에 SSL 인증서를 구성하는 방법에 대한 질문이 있다면 [IIS에서 SSL을 설정하는 방법](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis) 문서를 참조하세요.

웹 서비스 SDK는 SSL 인증서로 보호되어야 합니다. 이 작업을 위해 자체 서명된 인증서를 사용해도 됩니다. SSL 연결을 시작할 때 해당 인증서를 신뢰할 수 있도록 사용자 포털 웹 서버에 있는 로컬 컴퓨터 계정의 "신뢰할 수 있는 루트 인증 기관" 저장소로 인증서를 가져옵니다.

![MFA 서버 구성 설정 웹 서비스 SDK](./media/howto-mfaserver-deploy-mobileapp/sdk.png)

### <a name="install-the-service"></a>서비스 설치

1. **MFA 서버에서** 설치 경로를 찾습니다.
2. Azure MFA 서버를 설치한 폴더로 이동합니다. 기본값은 **C:\Program Files\Azure Multi-factor Authentication**입니다.
3. 설치 파일 **MultiFactorAuthenticationMobileAppWebServiceSetup64**를 찾습니다. 인터넷 연결 서버가 **아니면** 인터넷 연결 서버에 설치 파일을 복사합니다.
4. MFA 서버가 인터넷에 연결되지 **않으면** **인터넷 연결 서버**로 전환합니다.
5. **MultiFactorAuthenticationMobileAppWebServiceSetup64** 설치 파일을 관리자로 실행하고, 필요한 경우 사이트를 변경하고, 원하는 경우 가상 디렉터리를 짧은 이름으로 변경합니다.
6. 설치가 완료되면 **C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService**(또는 가상 디렉터리 이름에 따른 적절한 디렉터리)로 이동한 후 Web.Config 파일을 편집합니다.

   * **"WEB_SERVICE_SDK_AUTHENTICATION_USERNAME"** 키를 찾고 **value=""** 를 **value="DOMAIN\User"** 로 변경합니다. 여기서 DOMAIN\User는 "PhoneFactor Admins" 그룹의 일부인 서비스 계정입니다.
   * **"WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD"** 키를 찾고 **value=""** 를 **value="Password"** 로 변경합니다. 여기서 암호는 이전 줄에 입력한 서비스 계정의 암호입니다.
   * **pfMobile App Web Service_pfwssdk_PfWsSdk** 설정을 찾아서 **http://localhost:4898/PfWsSdk.asmx**의 값을 웹 서비스 SDK URL(예: https://mfa.contoso.com/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx))로 변경합니다.
   * Web.Config 파일을 저장하고 메모장을 닫습니다.

   > [!NOTE]
   > 이 연결에 SSL이 사용되므로 **IP 주소가 아니라** **FQDN(정규화된 도메인 이름)** 으로 웹 서비스 SDK를 참조해야 합니다. SSL 인증서는 FQDN에 대해 발급되었으며 사용된 URL은 인증서의 이름과 일치해야 합니다.

7. 모바일 앱 웹 서비스가 설치된 웹 사이트가 공개적으로 서명된 인증서에 아직 바인딩되지 않았으면 서버에 인증서를 설치하고 IIS 관리자를 연 다음 웹 사이트에 인증서를 바인딩합니다.
8. 컴퓨터에서 웹 브라우저를 열고 모바일 앱 웹 서비스가 설치된 URL(예: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService))로 이동합니다. 인증서 경고 또는 오류가 표시되지 않는지 확인합니다.
9. 웹 서비스 SDK에서 사용할 수 있는 방법에 대한 자세한 내용은 MFA 서버 도움말 파일을 참조하세요.
10. 모바일 앱 웹 서비스가 설치되었으므로 포털을 사용하도록 Azure Multi-Factor Authentication 서버를 구성해야 합니다.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Azure Multi-Factor Authentication 서버에서 모바일 앱 설정 구성

1. Multi-Factor Authentication 서버 콘솔에서 사용자 포털 아이콘을 클릭합니다. 사용자가 자신의 인증 방법을 제어할 수 있도록 허용되면 설정 탭의 **사용자가 방법을 선택할 수 있도록 허용**에서 **모바일 앱**을 선택합니다. 이 기능을 사용하지 않으면 최종 사용자는 지원 센터에 문의하여 Mobile App에 대한 정품 인증을 완료해야 합니다.
2. **사용자가 모바일 앱을 활성화할 수 있도록 허용** 상자를 선택합니다.
3. **사용자 등록 허용** 상자를 선택합니다.
4. **Mobile App** 아이콘을 클릭합니다.
5. v8.0 이상을 사용 중인 경우 다음 단계를 건너뜁니다. **모바일 앱 웹 서비스 URL:** 필드에서 MultiFactorAuthenticationMobileAppWebServiceSetup64를 설치할 때 생성된 가상 디렉터리로 사용되는 URL(예: https://mfa.contoso.com/MultiFactorAuthMobileAppWebService/))을 입력합니다.
6. **계정 이름** 필드를 이 계정의 모바일 응용 프로그램에서 표시할 회사 또는 조직 이름으로 채웁니다.
   ![MFA 서버 구성 Mobile App 설정](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication 및 타사 VPN을 사용한 고급 시나리오](howto-mfaserver-nps-vpn.md)
