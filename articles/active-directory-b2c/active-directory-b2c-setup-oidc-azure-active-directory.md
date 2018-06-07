---
title: 'Azure Active Directory B2C: 기본 제공 정책을 사용하여 Azure AD 공급자 추가 | Microsoft Docs'
description: Open ID Connect의 ID 공급자(Azure AD)를 추가하는 방법 알아보기
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 7dac9545-d5f1-4136-a04d-1c5740aea499
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/27/2018
ms.author: parja
ms.openlocfilehash: 52a752df9cf199acf39596f49e7368bce27a8158
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2018
ms.locfileid: "32309018"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Azure Active Directory B2C: 기본 제공 정책을 통해 Azure AD 계정을 사용하여 로그인

>[!NOTE]
> 이 기능은 공개 미리 보기 상태입니다. 프로덕션 환경에서는 이 기능을 사용하지 마세요.

이 문서에서는 특정 Azure AD(Azure Active Directory) 조직의 기본 제공 정책에서 사용자에 대한 로그인을 사용하도록 설정하는 방법에 대해 설명합니다.

## <a name="create-an-azure-ad-app"></a>Azure AD 앱 만들기

특정 Azure AD 조직의 사용자에 대한 로그인을 사용하도록 설정하려면 조직의 Azure AD 테넌트 내에 응용 프로그램을 등록해야 합니다.

>[!NOTE]
> 조직의 Azure AD 테넌트로 "contoso.com"을 사용하고, 다음 지침에서는 "fabrikamb2c.onmicrosoft.com"을 Azure AD B2C 테넌트로 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 위쪽 막대에서 계정을 선택합니다. **디렉터리** 목록에서 응용 프로그램을 등록할 조직의 Azure AD 테넌트(contoso.com)를 선택합니다.
1. 왼쪽 창에서 **모든 서비스**를 선택하고 “앱 등록”을 검색합니다.
1. **새 응용 프로그램 등록**을 선택합니다.
1. 응용 프로그램의 이름(예: `Azure AD B2C App`)을 입력합니다.
1. 응용 프로그램 종류에 대해 **웹앱/API**를 선택합니다.
1. **로그온 URL**에 대해 다음 URL을 입력합니다. 여기서 `yourtenant`는 Azure AD B2C 테넌트의 이름(`fabrikamb2c.onmicrosoft.com`)으로 바뀝니다.

    >[!NOTE]
    >"yourtenant"의 값은 **로그온 URL**에서 모두 소문자여야 합니다.

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. 응용 프로그램 ID를 저장합니다. 이 ID를 다음 섹션에서 클라이언트 ID로 사용하게 됩니다.
1. **설정** 블레이드 아래에서 **키**를 선택합니다.
1. **암호** 섹션 아래에 **키 설명**을 입력하고 **기간**을 “만료되지 않음”으로 설정합니다. 
1. **저장**을 클릭한 다음, 결과로 나타나는 키 **값**을 적어 둡니다. 이 값을 다음 섹션에서 클라이언트 암호로 사용합니다.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>테넌트에서 Azure AD를 ID 공급자로 구성

1. 위쪽 막대에서 계정을 선택합니다. **디렉터리** 목록에서 Azure AD B2C 테넌트(fabrikamb2c.onmicrosoft.com)를 선택합니다.
1. Azure Portal에서[Azure AD B2C 설정 메뉴로 이동](active-directory-b2c-app-registration.md#navigate-to-b2c-settings)합니다.
1. Azure AD B2C 설정 메뉴에서 **ID 공급자**를 클릭합니다.
1. 블레이드의 위쪽에서 **+추가**를 클릭합니다.
1. ID 공급자 구성에 친숙한 **이름** 을 제공합니다. 예를 들어, “Contoso Azure AD”를 입력합니다.
1. **ID 공급자 유형**을 클릭하고 **Open ID Connect**를 선택한 다음, **확인**을 클릭합니다.
1. **이 ID 공급자 설정**을 클릭합니다.
1. **메타데이터 URL**에 대해 다음 URL을 입력합니다. 여기서 `yourtenant`는 Azure AD 테넌트의 이름(`contoso.com`)으로 대체됩니다.

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. **클라이언트 ID** 및 **클라이언트 암호**의 경우 이전 섹션의 응용 프로그램 ID 및 키를 입력합니다.
1. **범위**에서 기본값인 `openid`로 유지합니다.
1. **응답 유형**에서 기본값인 `code`를 유지합니다.
1. **응답 모드**에서 기본값인 `form_post`를 유지합니다.
1. 선택적으로 **도메인**(예: `ContosoAD`)의 값을 입력합니다. 이 값은 요청에서 *domain_hint*를 사용하여 이 ID 공급자를 참조할 때 사용할 값입니다. 
1. **확인**을 클릭합니다.
1. **이 ID 공급자의 클레임 매핑**을 클릭합니다.
1. **사용자 ID**에 `oid`를 입력합니다.
1. **표시 이름**에 `name`을 입력합니다.
1. **이름**에 `given_name`을 입력합니다.
1. **성**에 `family_name`을 입력합니다.
1. **메일**에 `unique_name`을 입력합니다.
1. **확인**을 클릭한 후 **만들기**를 클릭하여 구성을 저장합니다.

## <a name="next-steps"></a>다음 단계

새로 만든 Azure AD ID 공급자를 기본 제공 정책에 추가하고 [AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com)에 피드백을 제공합니다.
