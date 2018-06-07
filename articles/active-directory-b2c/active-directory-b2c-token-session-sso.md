---
title: 토큰, 세션 및 Single Sign-On 구성 - Azure AD B2C | Microsoft Docs
description: Azure Active Directory B2C에서 토큰, 세션 및 Single Sign-On 구성
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: 925313b6f2a00826f2ec8086457315c60f70b007
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure Active Directory B2C: 토큰, 세션 및 Single Sign-On 구성

이 기능은 다음의 [정책별](active-directory-b2c-reference-policies.md)세분화된 제어를 제공합니다.

1. Azure AD(Azure Active Directory) B2C에서 내보낸 보안 토큰의 수명.
2. Azure AD B2C에서 관리하는 웹 응용 프로그램 세션의 수명.
3. Azure AD B2C에서 내보낸 보안 토큰의 중요한 클레임 형식
4. B2C 테넌트에 있는 여러 앱 및 정책의 SSO(Single Sign-On) 동작.

기본 제공 정책의 경우 다음과 같이 Azure AD B2C 디렉터리에서 이 기능을 사용할 수 있습니다.

1. 다음 단계에 따라 Azure Portal의 [B2C 기능 메뉴로 이동합니다](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. **등록 또는 로그인 정책**을 클릭합니다. *참고: **등록 또는 로그인 정책***뿐만 아니라 모든 정책 유형에서 이 기능을 사용할 수 있습니다.
3. 클릭하여 정책을 엽니다. 예를 들어 **B2C_1_SiUpIn**을 클릭합니다.
4. 메뉴 위쪽에서 **편집**을 클릭합니다.
5. **토큰, 세션 및 Single Sign-On 구성**을 클릭합니다.
6. 원하는 변경 사항을 적용합니다. 이후 섹션에서 사용할 수 있는 속성에 대해 알아봅니다.
7. **확인**을 클릭합니다.
8. 메뉴 위쪽에서 **저장**을 클릭합니다.

## <a name="token-lifetimes-configuration"></a>토큰 수명 구성

Azure AD B2C는 보호된 리소스에 대한 보안 액세스를 활성화하도록 [OAuth 2.0 권한 부여 프로토콜](active-directory-b2c-reference-protocols.md)을 지원합니다. 이 지원을 구현하기 위해 Azure AD B2C는 다양한 [보안 토큰](active-directory-b2c-reference-tokens.md)을 내보냅니다. 다음은 Azure AD B2C에서 내보낸 보안 토큰의 수명을 관리하는 데 사용할 수 있는 속성입니다.

* **액세스 및 ID 토큰 수명(분)**: OAuth 2.0 전달자 토큰의 수명은 보호된 리소스에 대한 액세스를 얻는 데 사용됩니다.
  * 기본값: 60분.
  * 최소(포함) = 5분.
  * 최대(포함) = 1440분.
* **새로 고침 토큰 수명(일)**: 새로 고침 토큰을 새 액세스 또는 ID 토큰(필요에 따라 응용 프로그램에 `offline_access` 범위가 부여된 경우 새로운 새로 고침 토큰)을 획득하는 데 사용할 수 있기 전까지의 최대 기간입니다.
  * 기본값 = 14일.
  * 최소(포함) = 1일.
  * 최대(포함) = 90일.
* **새로 고침 토큰 슬라이딩 창 수명(일)**: 이 기간이 경과한 후 사용자는 응용 프로그램에서 가져온 가장 최근 새로 고침 토큰의 유효 기간과 관계 없이 강제로 다시 인증합니다. 스위치가 **제한된**으로 설정된 경우 제공될 수 있습니다. **새로 고침 토큰 수명(일)** 값보다 크거나 같아야 합니다. 스위치가 **제한 없는**으로 설정된 경우 특정 값을 제공할 수 없습니다.
  * 기본값 = 90일.
  * 최소(포함) = 1일.
  * 최대(포함) = 365일.

다음은 이러한 속성을 사용하여 활성화할 수 있는 몇 가지 사용 사례입니다.

* 응용 프로그램에서 지속적으로 활성 상태인 한 사용자가 모바일 응용 프로그램에 무기한으로 로그인 상태를 유지하도록 허용합니다. 로그인 정책에서 **새로 고침 토큰 슬라이딩 창 수명(일)** 스위치를 **제한 없는**으로 설정하여 이를 수행할 수 있습니다.
* 적절한 액세스 토큰 수명을 설정하여 업계의 보안 및 규정 준수 요구 사항을 충족합니다.

    > [!NOTE]
    > 이러한 설정을 암호 재설정 정책에는 사용할 수 없습니다.
    > 
    > 

## <a name="token-compatibility-settings"></a>토큰 호환성 설정

Azure AD B2C에서 내보낸 보안 토큰에서 중요한 클레임에 대한 형식을 변경했습니다. 이는 표준 프로토콜 지원을 개선하고 타사 ID 라이브러리와의 상호 운용성을 향상시키기 위해 수행되었습니다. 그러나 기존 앱을 손상시키지 않기 위해 고객이 필요에 따라 옵트인할 수 있도록 다음과 같은 속성을 만들었습니다.

* **발급자(iss) 클레임**: 토큰을 발급한 Azure AD B2C 테넌트를 식별합니다.
  * `https://login.microsoftonline.com/{B2C tenant GUID}/v2.0/`: 기본값입니다.
  * `https://login.microsoftonline.com/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/`: 이 값에는 B2C 테넌트 및 토큰 요청에 사용된 정책에 대한 ID가 포함됩니다. 앱이나 라이브러리에 [OpenID Connect 검색 1.0 사양](http://openid.net/specs/openid-connect-discovery-1_0.html)과 호환되도록 Azure AD B2C가 필요한 경우 이 값을 사용합니다.
* **주체(sub) 클레임**: 토큰에서 정보를 어설션하는 엔터티, 즉 사용자를 식별합니다.
  * **ObjectID**: 기본값입니다. 디렉터리에 있는 사용자의 개체 ID를 토큰의 `sub` 클레임에 채웁니다.
  * **지원되지 않음**: 이전 버전과의 호환성을 위해서만 제공되므로 가능한 한 빨리 **ObjectID**로 전환하는 것이 좋습니다.
* **정책 ID를 나타내는 클레임**: 토큰 요청에 사용된 정책 ID가 채워지는 클레임 유형을 식별합니다.
  * **tfp**: 기본값입니다.
  * **acr**: 이전 버전과의 호환성을 위해서만 제공되므로 가능한 한 빨리 `tfp`로 전환하는 것이 좋습니다.

## <a name="session-behavior"></a>세션 동작

Azure AD B2C는 웹 응용 프로그램에 대한 보안 로그인을 활성화하도록 [OpenID Connect 인증 프로토콜](active-directory-b2c-reference-oidc.md)을 지원합니다. 다음은 웹 응용 프로그램 세션을 관리하는 데 사용할 수 있는 속성입니다.

* **웹앱 세션 수명(분)**: 인증 성공 시 사용자의 브라우저에 저장된 Azure AD B2C 세션 쿠키의 수명입니다.
  * 기본값: 1440분.
  * 최소(포함) = 15분.
  * 최대(포함) = 1440분.
* **웹앱 세션 제한 시간**: 이 스위치가 **절대**로 설정된 경우 사용자는 **웹앱 세션 수명(분)**에서 지정한 기간이 경과된 후 강제로 다시 인증합니다. 이 스위치가 **롤링** (기본 설정)으로 설정된 경우 사용자는 웹 응용 프로그램에서 지속적으로 활성 상태인 한 로그인 상태로 유지됩니다.

다음은 이러한 속성을 사용하여 활성화할 수 있는 몇 가지 사용 사례입니다.

* 적절한 웹 응용 프로그램 세션 수명을 설정하여 업계의 보안 및 규정 준수 요구 사항을 충족합니다.
* 사용자가 웹 응용 프로그램의 높은 수준의 보안 일부와 상호 작용하는 동안 설정 기간 후 강제로 다시 인증합니다. 

    > [!NOTE]
    > 이러한 설정을 암호 재설정 정책에는 사용할 수 없습니다.
    > 
    > 

## <a name="single-sign-on-sso-configuration"></a>SSO(Single Sign-On) 구성
B2C 테넌트에 여러 응용 프로그램 및 정책이 있는 경우 **Single Sign-On 구성** 속성을 사용하여 이들 간에 사용자 상호 작용을 관리할 수 있습니다. 다음 설정 중 하나에 속성을 설정할 수 있습니다.

* **테넌트**: 기본 설정입니다. 이 설정을 사용하여 B2C 테넌트의 여러 응용 프로그램 및 정책이 동일한 사용자 세션을 공유할 수 있습니다. 예를 들어 사용자가 응용 프로그램, Contoso Shopping에 로그인하면 액세스의 관점에서 볼 때 다른 앱인 Contoso Pharmacy에도 원활하게 로그인할 수 있습니다.
* **응용 프로그램**: 다른 응용 프로그램과 독립적으로 응용 프로그램에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 동일한 B2C 테넌트에 있는 다른 응용 프로그램인 Contoso Shopping에 이미 로그인한 경우라하더라도 (동일한 자격 증명을 사용하여) 사용자가 Contoso Pharmacy에 로그인하려는 경우가 있습니다. 
* **정책**: 이를 사용하는 응용 프로그램과 독립적으로 정책에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 사용자가 이미 로그인하고 MFA(다단계 인증) 단계를 완료한 경우 정책에 연결된 세션이 만료되지 않는 한 여러 응용 프로그램의 높은 수준의 보안 부분에 대한 액세스를 부여 받을 수 있습니다.
* **비활성화**: 사용자가 모든 정책 실행에서 전체 사용자 과정을 통해 강제로 실행하도록 합니다. 예를 들어 전체 시간 동안 단일 사용자가 로그인 상태로 남아 있더라도 여러 사용자가 응용 프로그램(공유 데스크톱 시나리오에서)에 로그인할 수 있습니다.

    > [!NOTE]
    > 이러한 설정을 암호 재설정 정책에는 사용할 수 없습니다.
    > 
    > 

