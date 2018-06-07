---
title: 'Azure Active Directory B2C: 기본 제공 정책 | Microsoft Docs'
description: Azure Active Directory B2C의 확장 가능한 정책 프레임워크 및 다양한 정책 형식을 만드는 방법에 대한 항목
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/26/2017
ms.author: davidmu
ms.openlocfilehash: 424186a0acfe17cd7cb96f3ba7f8201e8b2b38ec
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200351"
---
# <a name="azure-active-directory-b2c-built-in-policies"></a>Azure Active Directory B2C: 기본 제공 정책


Azure Active Directory(Azure AD) B2C의 확장할 수 있는 정책 프레임워크는 서비스의 핵심 장점입니다. 정책은 등록, 로그인 또는 프로필 편집과 같은 소비자 ID 환경을 완벽하게 설명합니다. 예를 들어 등록 정책을 사용하면 다음 설정을 구성하여 동작을 제어할 수 있습니다.

* 소비자가 응용 프로그램에 등록하는 데 사용할 수 있는 계정 유형(Facebook과 같은 소셜 계정 또는 이메일 주소와 같은 로컬 계정)
* 등록 시 소비자로부터 수집할 특성(예: 이름, 우편 번호 및 신발 크기)
* Azure Multi-Factor Authentication 사용
* 모든 등록 페이지의 모양과 느낌
* 정책 실행이 완료될 때 응용 프로그램에서 받는 정보(토큰에서 클레임으로 매니페스트됨)

테넌트에 다른 형식의 여러 정책을 만들고 필요에 따라 응용 프로그램에서 사용할 수 있습니다. 응용 프로그램에 정책을 다시 사용할 수 있습니다. 이러한 유연성을 통해 개발자가 코드를 변경하지 않거나 최소로 변경하여 소비자 ID 환경을 정의하고 수정할 수 있습니다.

간단한 개발자 인터페이스를 통해 정책을 사용할 수 있습니다. 응용 프로그램은 표준 HTTP 인증 요청을 사용하여 정책을 트리거하고(요청에 정책 매개 변수 전달) 사용자 지정 토큰을 응답으로 받습니다. 예를 들어 등록 정책을 호출하는 요청과 로그인 정책을 호출하는 요청 간의 유일한 차이점은 "p" 쿼리 문자열 매개 변수에 사용되는 정책 이름에 있습니다.

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up policy

```

```

https://login.microsoftonline.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in policy

```

## <a name="create-a-sign-up-or-sign-in-policy"></a>등록 또는 로그인 정책 만들기

이 정책은 단일 구성으로 등록 및 로그인 환경을 모두 처리합니다. 소비자는 컨텍스트에 따라 올바른 경로(등록 또는 로그인)를 진행합니다. 또한 성공적인 등록 또는 로그인 시 응용 프로그램에서 수신할 토큰 내용도 설명합니다.  **등록 또는 로그인** 정책에 대한 코드 샘플은 [여기에서 사용할 수 있습니다](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  **등록** 정책 및 **로그인** 정책 대신 이 정책을 사용하는 것이 좋습니다.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-policy"></a>등록 정책 만들기

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-policy"></a>로그인 정책 만들기

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-policy"></a>프로필 편집 정책 만들기

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-policy"></a>암호 재설정 정책 만들기

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-policies"></a>미리 보기 정책

새 기능을 릴리스할 때 일부 기능은 기존 정책에 사용할 수 없습니다.  이러한 정책이 GA에 돌입하면 기존 버전을 같은 종류의 최신 버전으로 교체할 계획입니다.  기존 정책은 변경되지 않으며, 새 기능을 사용하려면 새 정책을 만들어야 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-do-i-link-a-sign-up-or-sign-in-policy-with-a-password-reset-policy"></a>등록 또는 로그인 정책을 암호 재설정 정책과 연결하려면 어떻게 하나요?
**등록 또는 로그인** 정책을 만드는 경우(로컬 계정 사용) 환경의 첫 번째 페이지에서 **암호 찾기** 링크가 표시됩니다. 이 링크를 클릭해도 암호 재설정 정책이 자동으로 트리거되지는 않습니다. 

대신 **`AADB2C90118`** 오류 코드가 앱에 반환됩니다. 앱에서 특정 암호 재설정 정책을 호출하여 이 오류 코드를 처리해야 합니다. 자세한 내용은 [정책을 연결하는 방법을 보여 주는 샘플](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)(영문)을 참조하세요.

### <a name="should-i-use-a-sign-up-or-sign-in-policy-or-a-sign-up-policy-and-a-sign-in-policy"></a>등록 정책 또는 로그인 정책 또는 등록 정책 및 로그인 정책 중 어느 것을 사용해야 합니까?
**등록** 정책 및 **로그인** 정책에 대한 **등록 정책이나 로그인** 정책을 사용하는 것이 좋습니다.  

**등록 정책 또는 로그인** 정책에는 **로그인** 정책보다 많은 기능이 있습니다. 또한 페이지 UI 사용자 지정 기능을 사용할 수 있으며 지역화를 더 효율적으로 지원합니다. 

정책을 지역화할 필요가 없고, 브랜딩에 대한 약간의 사용자 지정 기능만 필요하고, 암호 재설정을 기본 제공하려는 경우에는 **로그인** 정책을 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
* [토큰, 세션 및 Single Sign-On 구성](active-directory-b2c-token-session-sso.md)
* [소비자를 등록하는 동안 전자 메일 확인 사용 안 함](active-directory-b2c-reference-disable-ev.md)

