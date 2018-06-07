---
title: Azure Active Directory v2.0 끝점 제한 사항 | Microsoft Docs
description: Azure AD v2.0 끝점의 제한 사항 목록입니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2017
ms.author: celested
ms.reviewer: dastrock
ms.custom: aaddev
ms.openlocfilehash: e026fd7021b39905d5392be55dbf3862cd307360
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="should-i-use-the-v20-endpoint"></a>v2.0 끝점을 사용해야 하나요?
Azure Active Directory와 통합되는 응용 프로그램을 빌드할 때 v2.0 끝점 및 인증 프로토콜이 사용자 요구를 충족하는지 결정해야 합니다. Azure Active Directory의 원래 끝점은 계속해서 완전히 지원되며 v2.0보다 더 많은 기능이 제공되는 측면이 있습니다. 그러나 v2.0 끝점은 개발자에게 [상당한 혜택을 소개](active-directory-v2-compare.md)합니다.

다음은 현 시점에서 개발자를 위한 간단한 권장 사항입니다.

* 응용 프로그램에서 개인 Microsoft 계정을 지원해야 하는 경우 v2.0 끝점을 사용하세요. 그러나 그전에 이 문서에 설명된 제한 사항을 이해해야 합니다.
* 응용 프로그램에서 Microsoft 회사 및 학교 계정만 지원해야 하는 경우 v2.0 끝점을 사용하지 마세요. 대신 [Azure AD 개발자 가이드](active-directory-developers-guide.md)를 참조하세요.

시간이 지남에 따라 v2.0 끝점은 여기에 나열된 제한 사항을 제거하게 되어 v2.0 끝점을 사용하도록 만듭니다. 그 동안 이 문서는 v2.0 끝점이 사용자에게 적합한지 결정하는 데 도움이 되도록 작성되었습니다. v2.0 끝점의 현재 상태를 반영하도록 이 문서를 계속 업데이트할 예정이므로 다시 확인하여 v2.0 기능에 대한 요구 사항을 다시 평가하세요.

v2.0 끝점을 사용하지 않는 기존 Azure AD 앱이 있는 경우 처음부터 시작할 필요가 없습니다. 나중에 v2.0 끝점에서 기존 Azure AD 응용 프로그램을 사용할 수 있는 방법을 제공할 예정입니다.

## <a name="restrictions-on-app-types"></a>앱 형식에 대한 제한 사항
다음 유형의 앱은 v2.0 끝점에서 현재 지원되지 않습니다. 지원되는 앱 형식에 대한 설명은 [Azure Active Directory v2.0 끝점에 대한 앱 형식](active-directory-v2-flows.md)을 참조하세요.

### <a name="standalone-web-apis"></a>독립 실행형 Web API
v2.0 끝점을 사용하여 [OAuth 2.0으로 보안이 유지되는 Web API를 빌드](active-directory-v2-flows.md#web-apis)할 수 있습니다 그러나 해당 Web API는 동일한 응용 프로그램 ID를 가진 응용 프로그램에서만 토큰을 받을 수 있습니다. 다른 응용 프로그램 ID를 가진 클라이언트에서는 Web API에 액세스할 수 없습니다. 이 클라이언트는 Web API에 대한 권한을 요청하거나 얻을 수 없습니다.

동일한 응용 프로그램 ID를 가진 클라이언트의 토큰을 수락하는 Web API를 빌드하는 방법을 확인하려면 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션에서 v2.0 끝점 Web API 샘플을 참조하세요.

## <a name="restrictions-on-app-registrations"></a>앱 등록에 대한 제한 사항
현재 v2.0 끝점과 통합하려는 각 앱에 대해 새 [Microsoft 응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 앱 등록을 만들어야 합니다. 기존 Azure AD 또는 Microsoft 계정 앱은 v2.0 끝점과 호환되지 않습니다. 응용 프로그램 등록 포털이 아닌 다른 포털에 등록된 앱은 v2.0 끝점과 호환되지 않습니다. 향후 기존 응용 프로그램을 v2.0 앱으로 사용할 수 있는 방법을 제공할 예정입니다. 그러나 현재는 기존 앱에서 v2.0 끝점을 사용할 수 있는 마이그레이션 경로가 없습니다.

또한 [응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 만든 앱 등록에는 다음과 같은 주의 사항이 있습니다.

* 응용 프로그램 ID당 두 개의 앱 암호만 허용됩니다.
* 개인 Microsoft 계정이 있는 사용자가 등록한 앱 등록은 단일 개발자 계정으로만 보고 관리할 수 있습니다. 여러 개발자 간에 공유할 수 없습니다. 여러 개발자 간에 앱 등록을 공유하려면 Azure AD 계정으로 등록 포털에 로그인하여 응용 프로그램을 만들 수 있습니다.
* 허용된 리디렉션 URI 형식에 대한 몇 가지 제한 사항이 있습니다. 리디렉션 URI에 대한 자세한 내용은 다음 섹션을 참조하세요.

## <a name="restrictions-on-redirect-uris"></a>리디렉션 URI에 대한 제한
응용 프로그램 등록 포털에 등록된 앱은 현재 제한된 리디렉션 URI 값 집합으로 한정됩니다. 웹앱 및 서비스에 대한 리디렉션 URI는 스키마 `https`로 시작해야 하고 모든 리디렉션 URI 값은 단일 DNS 도메인을 공유해야 합니다. 예를 들어 다음 리디렉션 URI 중 하나가 있는 웹앱은 등록할 수 없습니다.

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

등록 시스템은 기존 리디렉션 URI의 전체 DNS 이름을 편집하려는 리디렉션 URI의 DNS 이름과 비교합니다. 다음 조건 중 하나라도 충족되는 경우 DNS 이름을 추가하는 요청이 실패하게 됩니다.  

* 새 리디렉션 URI의 전체 DNS 이름이 기존 리디렉션 URI의 DNS 이름과 일치하지 않는 경우
* 새 리디렉션 URI의 전체 DNS 이름이 기존 리디렉션 URI의 DNS 이름의 하위 도메인이 아닌 경우

예를 들어 앱에 다음 리디렉션 URI가 있는 경우

`https://login.contoso.com`

다음과 같이 추가할 수 있습니다.

`https://login.contoso.com/new`

이 경우 DNS 이름이 정확히 일치합니다. 또는 다음을 수행할 수 있습니다.

`https://new.login.contoso.com`

이 경우 login.contoso.com의 DNS 하위 도메인을 참조합니다. 리디렉션 URI로 login-east.contoso.com 및 login-west.contoso.com을 사용하는 앱을 원하는 경우 다음 리디렉션 URI를 순서대로 추가해야 합니다.

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

뒤의 두 개는 첫 번째 리디렉션 URI의 하위 도메인이므로 추가할 수 있습니다. 이 제한은 향후 릴리스에서 제거될 예정입니다.

또한 특정 응용 프로그램에 대해 20개의 회신 URL만 있습니다.

응용 프로그램 등록 포털에 앱을 등록하는 방법을 알아보려면 [v2.0 끝점을 사용하여 앱을 등록하는 방법](active-directory-v2-app-registration.md)을 참조하세요.

## <a name="restrictions-on-libraries-and-sdks"></a>라이브러리 및 SDK에 대한 제한 사항
현재 v2.0 끝점에 대한 라이브러리 지원은 제한적입니다. 프로덕션 응용 프로그램에서 v2.0 끝점을 사용하려는 경우 다음 옵션이 있습니다.

* 웹 응용 프로그램을 작성하는 경우 Microsoft 일반 공급 서버 쪽 미들웨어를 안전하게 사용하여 로그인 및 토큰 유효성 검사를 수행할 수 있습니다. 여기에는 ASP.NET용 OWIN Open ID Connect 미들웨어 및 Node.js Passport 플러그 인이 포함됩니다. Microsoft 미들웨어를 사용하는 코드 샘플은 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션을 참조하세요.
* 데스크톱 또는 모바일 응용 프로그램을 작성하는 경우 미리 보기 MSAL(Microsoft 인증 라이브러리) 중 하나를 사용할 수 있습니다. 이러한 라이브러리는 프로덕션 지원 미리 보기이므로 프로덕션 응용 프로그램에서 안전하게 사용할 수 있습니다. 미리 보기 조건 및 사용 가능한 라이브러리에 대한 자세한 내용은 [인증 라이브러리 참조](active-directory-v2-libraries.md)에서 확인할 수 있습니다.
* Microsoft 라이브러리가 적용되지 않는 플랫폼의 경우 응용 프로그램 코드에서 프로토콜 메시지를 직접 전송 및 수신하여 v2.0 끝점과 통합할 수 있습니다. v2.0 OpenID Connect 및 OAuth 프로토콜은 [명시적으로 문서화](active-directory-v2-protocols.md)되어 있어 이러한 통합을 수행하는 데 도움이 됩니다.
* 마지막으로, v2.0 끝점과 통합하는 데 오픈 소스 Open ID Connect 및 OAuth 라이브러리를 사용할 수 있습니다. v2.0 프로토콜은 크게 변경되지 않고 다양한 오픈 소스 프로토콜 라이브러리와 호환되어야 합니다. 이러한 라이브러리의 사용 가능 여부는 언어 및 플랫폼마다 다릅니다. [Open ID Connect](http://openid.net/connect/) 및 [OAuth 2.0](http://oauth.net/2/) 웹 사이트는 주요 구현 목록을 유지 관리합니다. 자세한 내용은 [Active Directory v2.0 및 인증 라이브러리](active-directory-v2-libraries.md)와 v2.0 끝점으로 테스트한 오픈 소스 클라이언트 라이브러리 및 샘플 목록을 참조하세요.

## <a name="restrictions-on-protocols"></a>프로토콜에 대한 제한 사항
v2.0 끝점은 SAML 또는 WS-Federation을 지원하지 않으며 Open ID Connect 및 OAuth 2.0만 지원합니다. OAuth 프로토콜의 일부 특징과 기능은 v2.0 끝점에 통합되지 않았습니다. 이러한 프로토콜 특징과 기능은 현재 v2.0 끝점에서 *사용할 수 없습니다*.

* 전자 메일을 볼 수 있는 사용자의 승인을 획득하더라도 v2.0 끝점에서 발급한 ID 토큰에는 사용자에 대해 `email` 클레임이 포함되지 않습니다.
* OpenID Connect UserInfo 끝점은 v2.0 끝점에서 구현되지 않습니다. 그러나 이 끝점에서 잠재적으로 발생하는 모든 사용자 프로필 데이터는 Microsoft Graph `/me` 끝점에서 사용할 수 있습니다.
* v2.0 끝점은 ID 토큰에서 역할이나 그룹 클레임을 발급하도록 지원하지 않습니다.
* [OAuth 2.0 리소스 소유자 암호 자격 증명 부여](https://tools.ietf.org/html/rfc6749#section-4.3)는 v2.0 끝점에서 지원되지 않습니다.

또한 v2.0 끝점은 모든 형태의 SAML 또는 WS-Federation 프로토콜을 지원하지 않습니다.

v2.0 끝점에서 지원되는 프로토콜 기능의 범위를 더 잘 이해하려면 [OpenID Connect 및 OAuth 2.0 프로토콜 참조](active-directory-v2-protocols.md)를 참조하세요.

## <a name="restrictions-for-work-and-school-accounts"></a>회사 및 학교 계정에 대한 제한 사항
Windows 응용 프로그램에서 ADAL(Active Directory 인증 라이브러리)을 사용한 경우 SAML(Security Assertion Markup Language) 어설션 권한 부여를 사용하는 Windows 통합 인증을 활용했을 수 있습니다. 이 권한 부여를 통해 페더레이션된 Azure AD 테넌트의 사용자는 자격 증명을 입력하지 않고도 해당 온-프레미스 Active Directory 인스턴스로 자동으로 인증할 수 있습니다. SAML 어설션 권한 부여는 현재 v2.0 끝점에서 지원되지 않습니다.
