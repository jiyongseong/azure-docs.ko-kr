---
title: 'Azure AD Connect: Seamless Single Sign-On - 작동 방식 | Microsoft Docs'
description: 이 문서에서는 Azure Active Directory Seamless Single Sign-On 기능이 작동하는 방식을 설명합니다.
services: active-directory
keywords: Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소, SSO, Single Sign-on
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: 9d17a4038f2171b74c8ba1dbc21e8335e6893691
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory Seamless Single Sign-On: 기술 심층 분석

이 문서에서는 Azure Active Directory Seamless SSO(Seamless Single Sign-On) 기능이 어떻게 작동하는지에 대한 기술적인 정보에 대해 자세히 설명합니다.

## <a name="how-does-seamless-sso-work"></a>Seamless SSO 작동 방식

이 섹션은 다음 세 부분으로 이루어져 있습니다.
1. Seamless SSO 기능 설정
2. 웹 브라우저의 단일 사용자 로그인 트랜잭션이 Seamless SSO와 작동하는 방식
3. 네이티브 클라이언트의 단일 사용자 로그인 트랜잭션이 Seamless SSO와 작동하는 방식

### <a name="how-does-set-up-work"></a>기능을 설정하는 방식

Seamless SSO는 [여기](active-directory-aadconnect-sso-quick-start.md)서 보여 주듯이 Azure AD Connect를 통해 사용하도록 설정할 수 있습니다. 이 기능을 사용하도록 설정하는 동안 발생하는 단계는 다음과 같습니다.
- 온-프레미스 AD(Active Directory)에 Azure AD를 나타내는`AZUREADSSOACC`라는 컴퓨터 계정이 만들어집니다.
- 컴퓨터 계정의 Kerberos 암호 해독 키가 Azure AD와 안전하게 공유됩니다.
- 또한 Azure AD 로그인 중에 사용되는 두 개의 URL을 나타내기 위해 두 개의 Kerberos SPN(서비스 사용자 이름)도 만들어집니다.

>[!NOTE]
> 컴퓨터 계정과 Kerberos SPN은 Azure AD Connect를 통해 Azure AD와 동기화하는 각 AD 포리스트에서, 그리고 Seamless SSO의 대상이 되는 사용자에 대해 만들어집니다. 다른 컴퓨터 계정이 저장된 OU(조직 단위)로 `AZUREADSSOACC` 컴퓨터 계정을 이동하여 동일한 방식으로 관리되고 삭제되지 않도록 합니다.

>[!IMPORTANT]
>적어도 30일마다 `AZUREADSSOACC` 컴퓨터 계정의 [Kerberos 암호 해독 키를 롤오버](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account)하는 것이 좋습니다.

설정이 완료되면 Seamless SSO는 IWA(Windows 통합 인증)를 사용하는 다른 로그인과 동일한 방식으로 작동합니다.

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>웹 브라우저에서 Seamless SSO로 로그인하는 방식

웹 브라우저의 로그인 흐름은 다음과 같습니다.

1. 사용자가 회사 네트워크의 도메인 가입 회사 장치에서 웹 응용 프로그램(예: Outlook Web App - https://outlook.office365.com/owa/)에 액세스하려고 합니다.
2. 사용자가 아직 로그인하지 않은 경우 해당 사용자는 Azure AD 로그인 페이지로 리디렉션됩니다.
3. 사용자는 자신의 사용자 이름을 Azure AD 로그인 페이지에 입력합니다.

  >[!NOTE]
  >[특정 응용 프로그램](./active-directory-aadconnect-sso-faq.md#what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso)의 경우 2-3단계를 건너뜁니다.

4. Azure AD는 백그라운드에서 JavaScript를 사용하여 401 권한 없음 응답을 통해 브라우저에 Kerberos 티켓을 제공합니다.
5. 그런 다음 브라우저는 Active Directory에서 Azure AD를 나타내는 `AZUREADSSOACC` 컴퓨터 계정에 대한 티켓을 요청합니다.
6. Active Directory는 컴퓨터 계정을 찾아서 컴퓨터 계정의 비밀로 암호화된 Kerberos 티켓을 브라우저에 반환합니다.
7. 브라우저는 Active Directory에서 받은 Kerberos 티켓을 Azure AD에 전달합니다.
8. Azure AD는 이전에 공유한 키를 사용하여 회사 장치에 로그인한 사용자의 ID가 포함된 Kerberos 티켓을 암호 해독합니다.
9. 평가 후에 Azure AD는 응용 프로그램에 토큰을 반환하거나 사용자에게 Multi-Factor Authentication과 같은 추가 증명을 수행하도록 요청합니다.
10. 사용자 로그인에 성공하면 해당 사용자는 해당 응용 프로그램에 액세스할 수 있습니다.

다음 다이어그램은 관련된 모든 구성 요소와 단계를 보여 줍니다.

![Seamless Single Sign-On - 웹앱 흐름](./media/active-directory-aadconnect-sso/sso2.png)

Seamless SSO는 편의적인 기능입니다. 이는 SSO가 실패하면 로그인 환경은 일반 동작으로 돌아감을 의미합니다. 즉 사용자가 자신의 암호를 입력하여 로그인해야 합니다.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>네이티브 클라이언트에서 Seamless SSO로 로그인하는 방식

네이티브 클라이언트의 로그인 흐름은 다음과 같습니다.

1. 사용자가 회사 네트워크의 도메인 가입 회사 장치에서 네이티브 응용 프로그램(예: Outlook 클라이언트)에 액세스하려고 합니다.
2. 사용자가 아직 로그인되지 않은 경우 네이티브 응용 프로그램은 장치의 Windows 세션에서 사용자의 사용자 이름을 검색합니다.
3. 앱은 Azure AD에 사용자 이름을 전송하고, 테넌트의 WS-Trust MEX 끝점을 검색합니다.
4. 그런 후 WS-Trust MEX 끝점을 쿼리하여 통합된 인증 끝점을 사용할 수 있는지 확인합니다.
5. 4단계가 성공하면 Kerberos 챌린지가 발급됩니다.
6. 앱은 Kerberos 티켓을 검색할 수 있으면 Azure AD의 통합된 인증 끝점까지 전달합니다.
7. Azure AD는 Kerberos 티켓의 암호를 해독하고 유효한지 검사합니다.
8. Azure AD에서 사용자가 로그인되고, 앱으로 SAML 토큰이 발급됩니다.
9. 그런 후 앱은 Azure AD의 OAuth2 토큰 끝점으로 SAML 토큰을 제출합니다.
10. Azure AD는 SAML 토큰이 유효한지 검사하고, 지정된 리소스에 대한 액세스 토큰 및 새로 고침 토큰과 ID 토큰을 발급합니다.
11. 사용자는 앱의 리소스에 액세스할 수 있게 됩니다.

다음 다이어그램은 관련된 모든 구성 요소와 단계를 보여 줍니다.

![Seamless Single Sign On - 네이티브 앱 흐름](./media/active-directory-aadconnect-sso/sso14.png)

## <a name="next-steps"></a>다음 단계

- [**빠른 시작**](active-directory-aadconnect-sso-quick-start.md) - Azure AD Seamless SSO를 준비하고 실행합니다.
- [**FAQ(질문과 대답)**](active-directory-aadconnect-sso-faq.md) - 질문과 대답을 다루고 있습니다.
- [**문제 해결**](active-directory-aadconnect-troubleshoot-sso.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 새로운 기능 요청을 제출합니다.
