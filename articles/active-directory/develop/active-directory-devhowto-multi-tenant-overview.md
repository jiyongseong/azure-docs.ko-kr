---
title: 모든 Azure AD 사용자에게 로그인할 수 있는 앱을 작성하는 방법
description: 모든 Azure Active Directory 테넌트에서 사용자를 로그인할 수 있는 다중 테넌트 응용 프로그램을 빌드하는 방법을 보여 줍니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2018
ms.author: celested
ms.reviewer: elisol
ms.custom: aaddev
ms.openlocfilehash: fd02cde6327cb929d1b4c0c2e3d430d64645ca26
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157373"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>다중 테넌트 응용 프로그램 패턴을 사용하여 모든 Azure Active Directory 사용자를 로그인하는 방법
많은 조직에 SaaS(Software as a Service) 응용 프로그램을 제공하는 경우, 모든 Azure AD(Active Directory) 테넌트에서 로그인을 허용하도록 응용 프로그램을 구성할 수 있습니다. 이 구성에 대해 다중 테넌트에 응용 프로그램을 지정한다라고 합니다. 모든 Azure AD 테넌트의 사용자는 응용 프로그램에 계정을 사용하기로 동의한 후 응용 프로그램에 로그인할 수 있습니다. 

자체 계정 시스템을 갖춘 기존 응용 프로그램이 있거나 다른 클라우드 공급자로부터 다른 종류의 로그인을 지원하는 경우 테넌트의 Azure AD 로그인을 간단하게 추가할 수 있습니다. 응용 프로그램을 등록하고, OAuth2, OpenID Connect 또는 SAML을 통해 로그인 코드를 추가하고, 응용 프로그램에 ["Microsoft에 로그인" 단추][AAD-App-Branding]를 배치하기만 하면 됩니다.

> [!NOTE] 
> 이 문서에서는 사용자가 Azure AD에 대한 단일 테넌트 응용 프로그램을 빌드하는 것에 이미 익숙하다고 가정합니다. 그렇지 않은 경우 [개발자 가이드 홈페이지][AAD-Dev-Guide]에 있는 빠른 시작 중 하나를 시작해야 합니다.

다음과 같은 간단한 4 단계를 통해 응용 프로그램을 Azure AD 다중 테넌트 앱으로 변환할 수 있습니다.

1. [응용 프로그램 등록을 다중 테넌트로 업데이트합니다.](#update-registration-to-be-multi-tenant)
2. [요청을 /common 엔드포인트로 보내도록 코드를 업데이트합니다.](#update-your-code-to-send-requests-to-common)
3. [여러 발급자 값을 처리하도록 코드를 업데이트합니다.](#update-your-code-to-handle-multiple-issuer-values)
4. [사용자 및 관리자 동의를 이해하고 적절하게 코드를 변경합니다.](#understanding-user-and-admin-consent)

각 단계를 자세히 살펴보겠습니다. 또한 [이 다중 테넌트 샘플 목록][AAD-Samples-MT]으로 바로 건너뛸 수 있습니다.

## <a name="update-registration-to-be-multi-tenant"></a>등록을 다중 테넌트로 업데이트합니다.
기본적으로 Azure AD에서 웹앱/API 등록은 단일 테넌트입니다. [Azure Portal][AZURE-portal]에 있는 응용 프로그램 등록의 **속성** 창에서 **다중 테넌트** 스위치를 찾아 **예**로 설정하여 등록을 다중 테넌트에 지정할 수 있습니다.

응용 프로그램을 다중 테넌트에 지정하려면 먼저 Azure AD에 전역적으로 고유한 응용 프로그램의 앱 ID URI가 있어야 합니다. 앱 ID URI는 프로토콜 메시지에서 응용 프로그램을 식별하는 방법 중 하나입니다. 단일 테넌트 응용 프로그램의 경우 앱 ID URI이 해당 테넌트 내에서 고유한 것으로 충분합니다. 다중 테넌트 응용 프로그램의 경우, 앱 ID URI이 전역적으로 고유해야 Azure AD가 모든 테넌트에서 응용 프로그램을 찾을 수 있습니다. 앱 ID URI이 Azure AD 테넌트의 확인된 도메인과 일치하는 호스트 이름을 갖게 함으로써 전역 고유성이 적용됩니다. 기본적으로 Azure Portal을 통해 만든 앱에는 해당 앱을 만들 때 전역적으로 고유한 앱 ID URI가 설정되지만 이 값은 변경할 수 있습니다.

예를 들어, 테넌트의 이름이 contoso.onmicrosoft.com이라면 유효한 앱 ID URI은 `https://contoso.onmicrosoft.com/myapp`이 될 것입니다. 테넌트가 확인된 도메인 `contoso.com`를 가진 경우, 유효한 앱 ID URI은 또한 `https://contoso.com/myapp`이 됩니다. 앱 ID URI가 이 패턴을 따르지 않으면 응용 프로그램을 다중 테넌트로 설정하지 못합니다.

> [!NOTE] 
> 네이티브 클라이언트 등록 및 [v2 응용 프로그램](./active-directory-appmodel-v2-overview.md)은 기본적으로 다중 테넌트입니다. 따라서 이러한 응용 프로그램 등록을 다중 테넌트에 지정하는 작업은 수행할 필요가 없습니다.

## <a name="update-your-code-to-send-requests-to-common"></a>/common에 요청을 보내도록 코드를 업데이트합니다.
단일 테넌트 응용 프로그램에서 로그인 요청은 테넌트의 로그인 끝점에 전송됩니다. 예를 들어 contoso.onmicrosoft.com에 대한 엔드포인트는 `https://login.microsoftonline.com/contoso.onmicrosoft.com`입니다.

테넌트의 끝점에 보내진 요청은 그 테넌트에 있는 사용자 (또는 게스트)를 그 테넌트의 응용 프로그램으로 로그인하게 할 수 있습니다. 다중 테넌트 응용 프로그램을 사용할 경우, 응용 프로그램은 사용자가 어떤 테넌트에서 오는지 미리 알지 못하므로 요청을 테넌트 끝점에 보낼 수 없습니다. 대신, 요청은 모든 Azure AD 테넌트 간에 멀티플렉싱하는 엔드포인트(`https://login.microsoftonline.com/common`)로 보내집니다.

Azure AD에서 /common 엔드포인트의 요청을 받으면 사용자를 로그인하고, 이에 따라 사용자가 보낸 테넌트를 검색합니다. /common 끝점은 Azure AD에서 지원하는 OpenID Connect, OAuth 2.0, SAML 2.0 및 WS-Federation과 같은 모든 인증 프로토콜에서 작동합니다.

그 경우 응용 프로그램에 대한 로그인 응답에는 사용자를 나타내는 토큰이 들어 있습니다. 응용 프로그램은 토큰에 든 발급자 값을 보고 사용자가 어떤 테넌트에서 오는지 알게 됩니다. 응답이 /Common 끝점에서 반환될 때, 토큰의 발급자 값이 사용자의 테넌트에 해당합니다. 

> [!IMPORTANT]
> /common 엔드포인트는 테넌트도 아니고 발급자도 아니며 단지 멀티플렉서입니다. /Common을 사용할 때, 응용 프로그램에서 토큰의 유효성을 검사하는 논리는 이 점을 고려하도록 업데이트되어야 합니다. 

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>여러 발급자 값을 처리하는 코드를 업데이트합니다.
웹 응용 프로그램 및 웹 API는 Azure AD에서 토큰을 받아 유효성을 검사합니다. 

> [!NOTE]
> 네이티브 클라이언트 응용 프로그램은 Azure AD에 토큰을 요청하고 수신하는 동안에 API로 보내 유효성 검사를 합니다. 네이티브 응용 프로그램은 토큰의 유효성을 검사하지 않으며 그것을 불투명한 것으로 처리해야 합니다.

응용 프로그램이 Azure AD에서 수신한 토큰의 유효성을 검사하는 방법을 살펴보겠습니다. 단일 테넌트 응용 프로그램은 일반적으로 다음과 같은 끝점 값을 사용합니다.

    https://login.microsoftonline.com/contoso.onmicrosoft.com

그리고 이 값을 사용하여 다음과 같은 메타데이터 URL(이 경우 OpenID Connect)을 생성합니다.

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

이는 토큰의 유효성을 검사하는 데 사용할 두 가지 중요한 정보인 테넌트 서명 키와 발급자 값을 다운로드하기 위해서입니다. 각 Azure AD 테넌트에는 양식의 고유한 발급자 값이 있습니다.

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

여기서 GUID 값은 테넌트의 테넌트 ID 이름 바꾸기 안전 버전입니다. `contoso.onmicrosoft.com`에 대한 이전의 메타데이터 링크를 선택하면 문서에서 이 발급자 값을 볼 수 있습니다.

단일 테넌트 응용 프로그램이 토큰의 유효성을 검사하는 경우 메타데이터 문서에서 서명 키에 대해 토큰의 서명을 확인합니다. 이 테스트를 통해 토큰의 발급자 값이 메타데이터 문서에서 찾은 값과 일치하는지 확인할 수 있습니다.

/common 엔드포인트가 테넌트에 일치하지 않고 발급자가 아니기 때문에, /common에 대한 메타데이터의 발급자 값을 검사하면 실제값 대신 템플릿 기반 URL이 있습니다.

    https://sts.windows.net/{tenantid}/

따라서 다중 테넌트 응용 프로그램은 메타데이터의 발급자 값을 토큰의 `issuer` 값과 맞춰보는 것만으로는 토큰의 유효성을 검사할 수 없습니다. 다중 테넌트 응용 프로그램에는 발급자 값이 유효하고 발급자 값의 테넌트 ID 부분을 기반으로 하고 있지 않은지 결정하는 논리가 필요합니다. 

예를 들어, 다중 테넌트 응용 프로그램이 해당 서비스에 등록한 특정 테넌트로부터의 로그인 만을 허용한다면, 테넌트가 구독자 목록에 들어 있는지 확인하기 위해 발급자 값 또는 토큰의 `tid` 클레임 값을 확인해야 합니다. 다중 테넌트 응용 프로그램이 개인 만을 다루고 테넌트 기반으로 어떠한 액세스 결정도 하지 않는다면, 발급자 값 전체를 무시할 수 있습니다.

[다중 테넌트 샘플][AAD-Samples-MT]에서는 Azure AD 테넌트에서 로그인할 수 있도록 설정하기 위해 발급자 유효성 검사가 사용되지 않습니다.

## <a name="understanding-user-and-admin-consent"></a>사용자 및 관리자 동의 이해하기
사용자가 Azure AD에서 응용 프로그램에 로그인하려면 응용 프로그램이 사용자의 테넌트에 나타나야 합니다. 이를 통해 조직은 사용자가 테넌트로부터 응용 프로그램에 로그인할 때 고유한 정책을 적용하는 것과 같은 작업을 수행할 수 있습니다. 단일 테넌트 응용 프로그램의 경우 이 등록은 간단합니다. [Azure Portal][AZURE-portal]에서 응용 프로그램을 등록할 때 이루어집니다.

다중 테넌트 응용 프로그램의 경우, 응용 프로그램에 대한 초기 등록은 개발자가 사용한 Azure AD 테넌트에 있습니다. 다른 테넌트에서 사용자가 처음으로 응용 프로그램에 로그인할 때, Azure AD는 응용 프로그램에서 요청하는 사용 권한에 동의하는지 묻습니다. 동의한다면 *서비스 주체*라는 응용 프로그램의 표현이 사용자 테넌트에 생성되고 로그인은 계속 진행할 수 있습니다. 위임이 또한 사용자의 동의를 응용 프로그램에 기록하는 디렉토리에 만들어집니다. 응용 프로그램의 Application 및 ServicePrincipal 개체와 서로 간에 관계를 설정하는 방법에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체][AAD-App-SP-Objects]를 참조하세요.

![단일 계층 앱에 동의][Consent-Single-Tier] 

이 동의 환경이 응용 프로그램에서 요청한 사용 권한에 따른 영향을 받습니다. Azure AD에서는 응용 프로그램 전용 및 위임이라는 두 가지 유형의 권한을 지원합니다.

* 위임된 권한은 응용 프로그램에 사용자가 할 수 있는 작업의 하위 집합에 대해 로그인한 사용자 역할을 할 기능을 부여합니다, 예를 들어 응용 프로그램에 위임된 권한을 부여하여 로그인한 사용자의 일정을 읽게 할 수 있습니다.
* 응용 프로그램 전용 권한은 응용 프로그램의 id에 직접 부여됩니다. 예를 들어 누가 응용 프로그램에 로그인했는지에 상관없이 응용 프로그램에 응용 프로그램 전용 권한을 부여하여 테넌트의 사용자 목록을 읽게 할 수 있습니다.

일부 사용 권한은 일반 사용자가 동의할 수 있는 반면 또 다른 사용 권한은 테넌트 관리자의 동의가 필요합니다. 

### <a name="admin-consent"></a>관리자 동의
응용 프로그램 전용 권한은 테넌트 관리자의 동의를 항상 필요로 합니다. 응용 프로그램이 응용 프로그램 전용 사용 권한을 요청하고 사용자가 응용 프로그램에 로그인을 시도하는 경우 사용자가 동의할 수 없음을 알리는 오류 메시지가 나타납니다.

위임된 특정 권한은 또한 테넌트 관리자의 동의를 필요로 합니다. 예를 들어, 로그인한 사용자로 Azure AD에 쓰기 저장 기능은 테넌트 관리자의 동의가 필요합니다. 응용 프로그램 전용 권한과 같이, 일반 사용자가 관리자 동의가 필요한 위임된 권한을 요청하는 응용 프로그램에 로그인하려는 경우 응용 프로그램에 오류가 발생합니다. 권한에 관리자 동의가 필요한지 여부는 리소스를 게시한 개발자가 결정하며 해당 리스스에 대한 설명서에서 확인할 수 있습니다. [Azure AD Graph API][AAD-Graph-Perm-Scopes] 및 [Microsoft Graph API][MSFT-Graph-permision-scopes]에 대한 권한 설명서에는 관리자 동의가 필요한 권한이 나와 있습니다.

응용 프로그램이 관리자 동의가 필요한 권한을 사용할 경우, 관리자가 작업을 시작할 수 있도록 단추나 링크와 같은 제스처가 있어야 합니다. 응용 프로그램에서 이 작업에 대해 보내는 요청은 `prompt=admin_consent` 쿼리 문자열 매개 변수도 포함된 일반적인 OAuth2/OpenID Connect 권한 부여 요청입니다. 일단 관리자가 동의했고 서비스 주체가 고객 테넌트에 만들어졌다면 차후의 로그인 요청은 `prompt=admin_consent` 매개 변수를 필요로 하지 않습니다. 관리자가 요청된 권한이 허용된다고 결정했다면 테넌트의 다른 사용자들에게 그 시점 이후로 동의하라는 메시지가 표시되지 않습니다.

테넌트 관리자는 일반 사용자가 응용 프로그램에 동의하는 기능을 사용하지 않도록 설정할 수 있습니다. 이 기능이 사용되지 않는 경우 테넌트에서 응용 프로그램을 사용하려면 항상 관리자 동의가 필요합니다. 최종 사용자 동의를 사용하지 않도록 설정된 응용 프로그램을 테스트하려면 [Azure Portal][AZURE-portal]에 있는 **[사용자 설정](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** 섹션의 **엔터프라이즈 응용 프로그램** 아래에서 구성 스위치를 찾을 수 있습니다.

관리 동의가 필요하지 않은 사용 권한을 요청하는 응용 프로그램에서 `prompt=admin_consent` 매개 변수를 사용할 수도 있습니다. 예를 들어 응용 프로그램에 테넌트 관리자가 한 번 "등록"한 환경이 필요하고, 해당 시점에서 다른 사용자에게 동의를 요구하지 않는 경우가 있습니다.

응용 프로그램에 관리자 동의가 필요하고 관리자가 `prompt=admin_consent` 매개 변수를 보내지 않고 로그인하는 경우, 관리자가 응용 프로그램에 성공적으로 동의할 때 이는 **자신의 사용자 계정에만** 적용됩니다. 일반 사용자는 여전히 응용 프로그램에 로그인하거나 동의할 수 없습니다. 이 기능은 다른 사용자에게 액세스를 허용하기 전에 응용 프로그램을 탐색하는 기능을 테넌트 관리자에게 부여하고자 할 때 유용합니다.

> [!NOTE]
> 일부 응용 프로그램은 처음에는 일반 사용자가 동의할 수 있고 나중에는 응용 프로그램이 관리자를 참여시키고 관리자 동의가 필요한 권한을 요청할 수 있는 환경을 원합니다. 현재 Azure AD에서 v1 응용 프로그램 등록으로 이 작업을 수행할 수 있는 방법은 없습니다. 그러나 v2 엔드포인트를 사용하면 응용 프로그램에서 등록 시가 아니라 런타임에 권한을 요청할 수 있으므로 이 시나리오를 사용할 수 있습니다. 자세한 내용은 [v2 엔드포인트][AAD-V2-Dev-Guide]을 참조하세요.

### <a name="consent-and-multi-tier-applications"></a>동의 및 다중 계층 응용 프로그램
응용 프로그램은 여러 계층을 가질 수 있으며, 각 계층은 Azure AD에서 자체 등록에 의해 표현될 수 있습니다. 예를 들어, 웹 API를 호출하는 네이티브 응용 프로그램 또는 웹 API를 호출하는 웹 응용 프로그램. 두 경우 모두, 클라이언트(네이티브 앱 또는 웹앱)는 리소스(웹 API)를 호출하는 권한을 요청합니다. 클라이언트가 고객의 테넌트로 성공적으로 동의되려면 사용 권한을 요청한 모든 리소스가 고객의 테넌트에 이미 있어야 합니다. 이 조건이 충족되지 않으면, Azure AD는 리소스가 먼저 추가되어야 한다는 오류를 반환합니다.

**단일 테넌트의 여러 계층**

논리 응용 프로그램이 예를 들어 별도의 클라이언트와 리소스와 같은 두 개 이상의 응용 프로그램 등록으로 구성되어 있다면 이것이 문제일 수 있습니다. 우선 리소스를 고객 테넌트에 가져가려면 어떻게 해야 합니까? Azure AD에서는 클라이언트와 리소스를 한 번에 승인하여 이 문제를 해결합니다. 동의 페이지에서 클라이언트와 리소스 모두에서 요청한 전체 사용 권한을 사용자에게 표시합니다. 이 동작을 사용하도록 설정하려면 리소스의 응용 프로그램 등록에 클라이언트의 앱 ID가 해당 [응용 프로그램 매니페스트][AAD-App-Manifest]의 `knownClientApplications`로 포함되어야 합니다. 예: 

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

이는 이 문서의 뒷부분에 나오는 [관련 콘텐츠](#related-content) 섹션에 있는 다중 계층 네이티브 클라이언트 호출 웹 API 샘플에서 설명됩니다. 다음 다이어그램에서는 단일 테넌트에 등록된 다중 계층 응용 프로그램에 대한 동의 개요를 제공합니다.

![알려진 다중 계층 클라이언트 앱에 동의][Consent-Multi-Tier-Known-Client] 

**다중 테넌트의 여러 계층**

응용 프로그램의 다른 계층이 다른 테넌트에 등록되어 있다면 유사한 사례가 발생합니다. 예를 들어 Office 365 Exchange Online API를 호출하는 네이티브 클라이언트 응용 프로그램을 구축하는 경우를 생각해 보겠습니다. 네이티브 응용 프로그램을 개발하고, 그 후 네이티브 응용 프로그램이 고객 테넌트에서 실행되도록 하려면, Exchange Online 서비스 주체가 있어야 합니다. 이 경우에 개발자 및 고객이 자신의 테넌트에 서비스 주체가 생성되도록 하려면 Exchange Online를 구매해야 합니다. 

Microsoft 이외의 조직에서 빌드한 API의 경우, API 개발자는 고객이 자신의 테넌트에 대한 응용 프로그램에 동의하는 방법을 제공해야 합니다. 타사 개발자가 API를 작성하여 등록을 구현하는 웹 클라이언트로도 기능할 수 있도록 설계하는 것이 좋습니다. 다음을 수행합니다.

1. 이전 섹션에 따라 API에서 다중 테넌트 응용 프로그램 등록/코드 요구 사항을 구현하는지 확인합니다.
2. API의 범위/역할을 노출하는 것 외에도, 등록에 "로그인 및 사용자 프로필 읽기" Azure AD 권한(기본 제공)이 포함되어 있는지 확인합니다.
3. 앞에서 설명한 [관리자 동의](#admin-consent) 지침에 따라 웹 클라이언트에 로그인/등록 페이지를 구현합니다.
4. 사용자가 응용 프로그램에 동의하면 서비스 주체 및 동의 위임 링크가 해당 테넌트에 만들어지고 네이티브 응용 프로그램에서 API에 대한 토큰을 가져올 수 있습니다.

다음 다이어그램에서는 다른 테넌트에 등록된 다중 계층 응용 프로그램에 대한 동의 개요를 제공합니다.

![다중 계층 다자 앱에 동의][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>동의 철회
사용자와 관리자는 언제든지 응용 프로그램에 대한 동의를 해지할 수 있습니다.

* 사용자는 자신의 [액세스 패널 응용 프로그램][AAD-Access-Panel] 목록에서 개별 응용 프로그램을 제거하여 해당 응용 프로그램에 대한 액세스 권한을 취소합니다.
* 관리자는 [Azure Portal][AZURE-portal]의 [엔터프라이즈 응용 프로그램](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) 섹션을 사용하여 Azure AD에서 응용 프로그램을 제거하여 해당 응용 프로그램에 대한 액세스 권한을 철회합니다.

관리자가 테넌트의 모든 사용자에 대해 응용 프로그램에 동의하는 경우 사용자는 개별적으로 액세스를 해지할 수 없습니다. 관리자만이 액세스를 해지할 수 있으며 전체 응용 프로그램에 대해서만 해지할 수 있습니다.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>다중 테넌트 응용 프로그램 및 액세스 토큰 캐싱
다중 테넌트 응용 프로그램은 또한 Azure AD로 보호되는 API를 호출하는 액세스 토큰을 가져올 수도 있습니다. 다중 테넌트 응용 프로그램에서 ADAL(Active Directory 인증 라이브러리)을 사용하는 경우의 일반적인 오류는 먼저 /common을 사용하여 사용자에 대한 토큰을 요청하고, 응답을 받은 다음, 또 다시 /common을 사용하여 동일한 사용자에 대한 후속 토큰을 요청하는 것입니다. Azure AD의 응답은 /common이 아닌 테넌트에서 제공되므로 ADAL은 토큰을 테넌트에서 가져온 것으로 캐시합니다. 사용자에 대한 액세스 토큰을 가져오기 위한 /common에 대한 후속 호출은 캐시 항목이 누락되어, 사용자에게 다시 로그인하라는 메시지가 표시됩니다. 캐시 누락을 방지하려면 이미 로그인한 사용자에 대한 후속 호출이 테넌트의 끝점에 대해 있었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 모든 Azure AD 테넌트에서 사용자를 로그인할 수 있는 응용 프로그램을 빌드하는 방법을 알아보았습니다. 응용 프로그램 및 Azure AD 간에 SSO(Single Sign-On)를 사용하도록 설정되면 Office 365와 같은 Microsoft 리소스에서 노출되는 API에 액세스하도록 응용 프로그램을 업데이트할 수도 있습니다. 이렇게 하면 사용자에게 컨텍스트 정보(예: 프로필 사진 또는 다음 일정 약속)를 표시하는 등 개인 설정 환경을 응용 프로그램에 제공할 수 있습니다. Azure AD 및 Office 365 서비스(예: Exchange, SharePoint, OneDrive, OneNote, Planner, Excel 등)에 대한 API를 호출하는 방법에 대해 자세히 알아보려면 [Microsoft Graph API][MSFT-Graph-overview]를 방문하세요.

## <a name="related-content"></a>관련 콘텐츠
* [다중 테넌트 응용 프로그램 샘플][AAD-Samples-MT]
* [응용 프로그램에 대한 브랜딩 지침][AAD-App-Branding]
* [응용 프로그램 개체 및 서비스 주체 개체][AAD-App-SP-Objects]
* [Azure Active Directory와 응용 프로그램 통합][AAD-Integrating-Apps]
* [동의 프레임워크 개요][AAD-Consent-Overview]
* [Microsoft Graph API 권한 범위][MSFT-Graph-permision-scopes]
* [Azure AD Graph API 권한 범위][AAD-Graph-Perm-Scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














