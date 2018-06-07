---
title: 기본 제공 정책에 OpenID Connect ID 공급자 추가 - Azure AD B2C | Microsoft Docs
description: Azure AD B2C 내 기본 제공 정책에 OpenID Connect 공급자를 추가하는 방법을 알려주는 개요
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: parakhj
ms.assetid: 357d193a-e33b-469c-8a93-0a8f45a60a9f
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/27/2018
ms.author: davidmu
ms.openlocfilehash: 46223f1ec6b82828983861dae26ec8d777b77b86
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2018
ms.locfileid: "32308987"
---
# <a name="azure-active-directory-b2c-add-a-custom-openid-connect-identity-provider-in-built-in-policies"></a>Azure Active Directory B2C: 기본 제공 정책에 사용자 지정 OpenID Connect ID 공급자 추가

>[!NOTE]
> 이 기능은 공개 미리 보기 상태입니다. 프로덕션 환경에서는 이 기능을 사용하지 마세요.

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html)는 사용자를 안전하게 로그인하는 데 사용할 수 있는 OAuth 2.0을 기반으로 빌드된 인증 프로토콜입니다. 이 프로토콜을 사용하는 [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)와 같은 ID 공급자는 대부분 Azure AD B2C에서 지원됩니다. 이 문서에서는 사용자 지정 OpenID Connect ID 공급자를 기본 제공 정책에 추가하는 방법을 설명합니다.

## <a name="configuring-a-custom-openid-connect-identity-provider"></a>사용자 지정 OpenID Connect ID 공급자 구성

사용자 지정 OpenID Connect ID 공급자를 추가하려면 다음을 수행합니다.

1. 다음 단계에 따라 Azure Portal의 [Azure AD B2C 설정으로 이동](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)합니다.
1. **ID 공급자**를 클릭합니다.
1. **추가**를 클릭합니다.
1. **ID 공급자 유형**에서 **OpenID Connect**를 선택합니다.

### <a name="setting-up-the-openid-connect-identity-provider"></a>OpenID Connect ID 공급자 설정

#### <a name="metadata-url"></a>메타데이터 URL

사양에 따라 모든 OpenID Connect ID 공급자는 로그인을 수행하는 데 필요한 대부분의 정보를 포함하는 메타데이터 문서를 설명합니다. 여기에는 사용할 URL, 서비스의 공개 서명 키의 위치 등과 같은 정보가 포함됩니다. OpenID Connect 메타데이터 문서는 항상 `.well-known\openid-configuration`으로 끝나는 엔드포인트에 있습니다.

추가하려는 OpenID Connect ID 공급자의 경우 해당 메타데이터 URL을 입력합니다.

#### <a name="client-id-and-secret"></a>클라이언트 ID 및 비밀

사용자가 로그인할 수 있도록 허용하기 위해 ID 공급자는 개발자가 해당 서비스에 응용 프로그램을 등록하도록 요청합니다. 이 응용 프로그램에는 ID(**클라이언트 ID**라고 함) 및 **클라이언트 비밀**이 있습니다. ID 공급자에서 이러한 값을 복사하여 해당하는 필드에 입력합니다.

> [!NOTE]
> 클라이언트 비밀은 선택 사항입니다. 하지만 비밀을 사용하여 토큰에 대한 코드를 교환하는 [권한 부여 코드 흐름](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)을 사용하려는 경우에는 클라이언트 비밀을 입력해야 합니다.

#### <a name="scope"></a>범위

범위는 사용자 지정 ID 공급자로부터 수집하려는 정보 및 사용 권한을 정의합니다. ID 공급자로부터 ID 토큰을 받을 수 있도록 OpenID Connect 요청에 `openid` 범위 값이 포함되어 있어야 합니다. 사용자는 ID 토큰이 없으면 사용자 지정 ID 공급자를 사용하여 Azure AD B2C에 로그인할 수 없습니다.

다른 범위를 추가할 수 있습니다(공백으로 구분). 사용할 수 있는 다른 범위를 확인하려면 사용자 지정 ID 공급자의 설명서를 참조하세요.

#### <a name="response-type"></a>응답 형식

응답 유형은 초기 호출 시 사용자 지정 ID 공급자의 `authorization_endpoint`로 돌려 보내는 정보의 유형을 설명합니다. 

* `code`: [권한 부여 코드 흐름](http://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)에 따라 코드가 Azure AD B2C로 다시 반환됩니다. 그러면 Azure AD B2C는 `token_endpoint`를 호출하여 토큰에 대한 코드를 교환합니다.
* `token`: 액세스 토큰이 사용자 지정 ID 공급자에서 Azure AD B2C로 다시 반환됩니다.
* `id_token`: ID 토큰이 사용자 지정 ID 공급자에서 Azure AD B2C로 다시 반환됩니다.


#### <a name="response-mode"></a>응답 모드

응답 모드는 사용자 지정 ID 공급자에서 Azure AD B2C로 데이터를 다시 보내는 데 사용해야 하는 메서드를 정의합니다.

* `form_post`: 최상의 보안을 위해 이 응답 모드를 사용하는 것이 좋습니다. 응답이 `application/x-www-form-urlencoded` 형식을 사용하여 본문에서 인코딩되는 코드 또는 토큰으로 HTTP `POST` 메서드를 통해 전송됩니다.
* `query`: 코드 또는 토큰이 쿼리 매개 변수로 반환됩니다.


#### <a name="domain-hint"></a>도메인 힌트

도메인 힌트를 사용하면 사용자가 사용할 수 있는 ID 공급자의 목록 중에 선택하는 대신 지정된 ID 공급자의 로그인 페이지로 바로 건너뛸 수 있습니다. 이러한 종류의 동작을 허용하려면 도메인 힌트에 대한 값을 입력합니다.

사용자 지정 ID 공급자로 점프하려면 로그인을 위해 Azure AD B2C를 호출할 때 매개 변수 `domain_hint=<domain hint value>`를 사용자 요청의 끝에 추가합니다.


### <a name="mapping-the-claims-from-the-openid-connect-identity-provider"></a>OpenID Connect ID 공급자에서 클레임 매핑

사용자 지정 ID가 ID 토큰을 Azure AD B2C로 다시 보낸 후, Azure AD B2C는 수신된 토큰의 클레임을 Azure AD B2C가 인식 및 사용하는 클레임으로 매핑할 수 있어야 합니다. 

아래에 나온 각각의 매핑의 경우 사용자 지정 ID 공급자의 문서를 참조하여 ID 공급자의 토큰에 다시 반환되는 클레임에 대해 알아보세요.

* `User ID`: 로그인한 사용자에 대한 고유 식별자를 제공하는 클레임을 입력합니다.
* `Display Name`: 사용자에 대한 표시 이름 또는 전체 이름을 제공하는 클레임을 입력합니다.
* `Given Name`: 사용자의 이름을 제공하는 클레임을 입력합니다.
* `Surname`: 사용자의 성을 제공하는 클레임을 입력합니다.
* `Email`: 사용자의 이메일 주소를 제공하는 클레임을 입력합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 OpenID Connect ID 공급자를 [기본 제공 정책](active-directory-b2c-reference-policies.md)에 추가합니다.
