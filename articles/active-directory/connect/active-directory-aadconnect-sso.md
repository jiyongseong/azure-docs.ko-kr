---
title: 'Azure AD Connect: Seamless Single Sign-On | Microsoft Docs'
description: 이 항목에서는 Azure AD(Azure Active Directory) Seamless Single Sign-On 및 이를 사용하여 회사 네트워크 내의 회사 데스크톱 사용자에게 진정한 Single Sign-On을 제공하는 방법을 설명합니다.
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
ms.date: 03/22/2018
ms.author: billmath
ms.openlocfilehash: 6a106ba59f09ffc20f8461c5267ae06695df4cd0
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2018
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory Seamless Single Sign-On

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Azure Active Directory Seamless Single Sign-On이란?

Azure AD Seamless SSO(Azure Active Directory Seamless Single Sign-On)는 회사 네트워크에 연결된 회사 장치에 있을 때 사용자를 자동으로 서명합니다. 이 기능을 사용하도록 설정되면 사용자가 Azure AD에 로그인하는 데 암호를 입력할 필요가 없고, 일반적으로 사용자 이름으로도 입력할 수 있습니다. 추가 온-프레미스 구성 요소가 없어도 사용자가 클라우드 기반 응용 프로그램에 쉽게 액세스할 수 있습니다.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Seamless SSO는 [암호 해시 동기화](active-directory-aadconnectsync-implement-password-hash-synchronization.md) 또는 [통과 인증](active-directory-aadconnect-pass-through-authentication.md) 로그인 방법과 결합할 수 있습니다.

![Seamless Single Sign-On](./media/active-directory-aadconnect-sso/sso1.png)

>[!IMPORTANT]
>ADFS(Active Directory Federation Services)에는 Seamless SSO를 적용할 수 _없습니다_.

## <a name="key-benefits"></a>주요 이점

- *멋진 사용자 환경*
  - 사용자는 온-프레미스 및 클라우드 기반 응용 프로그램에 자동으로 로그인됩니다.
  - 사용자는 자신의 암호를 반복적으로 입력할 필요가 없습니다.
- *손쉬운 배포 및 관리*
  - 이 작업을 수행하기 위해 온-프레미스에 필요한 추가 구성 요소가 없습니다.
  - 모든 클라우드 인증 방법, 즉 [암호 해시 동기화](active-directory-aadconnectsync-implement-password-hash-synchronization.md) 또는 [통과 인증](active-directory-aadconnect-pass-through-authentication.md)과 함께 작동합니다.
  - 그룹 정책을 사용하여 일부 또는 모든 사용자에게 배포할 수 있습니다.
  - AD FS 인프라에 대한 필요 없이 Azure AD로 비-Windows 10 장치를 등록합니다. 이 기능을 사용하기 위해 [작업 공간 연결 클라이언트](https://www.microsoft.com/download/details.aspx?id=53554) 버전 2.1 이상이 필요합니다.

## <a name="feature-highlights"></a>주요 기능

- 로그인 사용자 이름은 온-프레미스 기본 사용자 이름(`userPrincipalName`) 또는 Azure AD Connect에 구성된 다른 특성(`Alternate ID`) 중 하나일 수 있습니다. Seamless SSO는 Kerberos 티켓에서 `securityIdentifier` 클레임을 사용하여 Azure AD에서 해당하는 사용자 개체를 조회하기 때문에 두 사용 사례가 작동합니다.
- Seamless SSO는 편의적인 기능입니다. 어떤 이유로든 실패하면 사용자 로그인 환경은 일반 동작으로 돌아갑니다. 즉 사용자가 로그인 페이지에 자신의 암호를 입력해야 합니다.
- 응용 프로그램(예: https://myapps.microsoft.com/contoso.com))이 Azure AD 로그인 요청에서 테넌트를 식별하는 `domain_hint`(OpenID Connect) 또는 `whr`(SAML) 매개 변수 또는 사용자를 식별하는 `login_hint` 매개 변수를 전달하면, 사용자는 사용자 이름이나 암호를 입력하지 않고 자동으로 로그인됩니다.
- 또한 응용 프로그램(예: https://contoso.sharepoint.com) )이 Azure AD의 공통 끝점(즉, https://login.microsoftonline.com/common/<...>) 대신 Azure AD의 테넌트 끝점(즉, https://login.microsoftonline.com/contoso.com/<..> 또는 https://login.microsoftonline.com/<tenant_ID>/<..>)으로 로그인 요청을 전송할 경우 사용자는 자동 로그온 환경을 사용할 수 있습니다.
- 로그아웃이 지원됩니다. 따라서 사용자는 Seamless SSO를 사용하여 자동으로 로그인되는 대신, 로그인하는 데 사용할 다른 Azure AD 계정을 선택할 수 있습니다.
- Office 365 클라이언트(16.0.8730.xxxx 이상)는 비대화형 흐름을 사용하여 지원됩니다.
- Azure AD Connect를 통해 사용하도록 설정할 수 있습니다.
- 무료 기능이며 이 기능을 사용하는 데는 Azure AD 유료 버전이 필요하지 않습니다.
- Kerberos 인증이 가능한 플랫폼 및 브라우저에서 [최신 인증](https://aka.ms/modernauthga)을 지원하는 웹 브라우저 기반 클라이언트 및 Office 클라이언트에서 지원됩니다.

| OS\Browser |Internet Explorer|Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|윈도우 10|예|아니오|예|예\*|해당 없음
|Windows 8.1|예|해당 없음|예|예\*|해당 없음
|Windows 8|예|해당 없음|예|예\*|해당 없음
|Windows 7|예|해당 없음|예|예\*|해당 없음
|Mac OS X|해당 없음|해당 없음|예\*|예\*|예\*

\*[추가 구성](active-directory-aadconnect-sso-quick-start.md#browser-considerations)이 필요합니다.

>[!NOTE]
>Windows 10의 경우 Azure AD를 통한 최적의 Single Sign-On 환경을 위해 [Azure AD 조인](../active-directory-azureadjoin-overview.md)을 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [**빠른 시작**](active-directory-aadconnect-sso-quick-start.md) - Azure AD Seamless SSO를 준비하고 실행합니다.
- [**기술 심층 분석**](active-directory-aadconnect-sso-how-it-works.md) - 이 기능의 작동 방식을 이해합니다.
- [**FAQ(질문과 대답)**](active-directory-aadconnect-sso-faq.md) - 질문과 대답을 다루고 있습니다.
- [**문제 해결**](active-directory-aadconnect-troubleshoot-sso.md) - 기능과 관련된 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 새로운 기능 요청을 제출합니다.
