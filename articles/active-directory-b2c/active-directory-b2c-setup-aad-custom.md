---
title: 'Azure Active Directory B2C: 사용자 지정 정책을 사용하여 Azure AD 공급자 추가 | Microsoft Docs'
description: Azure Active Directory B2C 사용자 지정 정책에 대해 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: mtillman
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 40943c135746925929daf7ebae4714ef70eeda51
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32140226"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-azure-ad-accounts"></a>Azure Active Directory B2C: Azure AD 계정을 사용하여 로그인

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 특정 Azure AD(Azure Active Directory) 조직의 사용자에 대한 로그인을 사용하도록 설정하는 방법에 대해 설명합니다.

## <a name="prerequisites"></a>필수 조건

[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md) 문서의 단계를 완료합니다.

이러한 단계는 다음과 같습니다.

1. Azure AD B2C(Azure Active Directory B2C) 테넌트 만들기
2. Azure AD B2C 응용 프로그램 만들기
3. 두 개의 정책 엔진 응용 프로그램 등록
4. 키 설정
5. 시작 팩 설정

## <a name="create-an-azure-ad-app"></a>Azure AD 앱 만들기

특정 Azure AD 조직의 사용자에 대한 로그인을 사용하도록 설정하려면 조직의 Azure AD 테넌트 내에 응용 프로그램을 등록해야 합니다.

>[!NOTE]
> 조직의 Azure AD 테넌트로 "contoso.com"을 사용하고, 다음 지침에서는 "fabrikamb2c.onmicrosoft.com"을 Azure AD B2C 테넌트로 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 위쪽 막대에서 계정을 선택합니다. **디렉터리** 목록에서 응용 프로그램을 등록하려는 조직의 Azure AD 테넌트(contoso.com)를 선택합니다.
3. 왼쪽 창에서 **더 많은 서비스**를 클릭하고 "앱 등록"을 검색합니다.
4. **새 응용 프로그램 등록**을 선택합니다.
5. 응용 프로그램의 이름(예: `Azure AD B2C App`)을 입력합니다.
6. 응용 프로그램 종류에 대해 **웹앱/API**를 선택합니다.
7. **로그온 URL**에 대해 다음 URL을 입력합니다. 여기서 `yourtenant`는 Azure AD B2C 테넌트의 이름(`fabrikamb2c.onmicrosoft.com`)으로 바뀝니다.

    >[!NOTE]
    >"yourtenant"의 값은 **로그온 URL**에서 모두 소문자여야 합니다.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

8. 응용 프로그램 ID를 저장합니다.
9. 새로 만든 응용 프로그램을 선택합니다.
10. **설정** 블레이드 아래에서 **키**를 선택합니다.
11. 키 설명을 입력하고, 기간을 선택한 다음, **저장**을 클릭합니다. 키 값이 표시됩니다. 이 키는 다음 섹션의 단계에서 사용되므로 복사합니다.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Azure AD B2C에 Azure AD 키 추가

Azure AD B2C 테넌트에 contoso.com 응용 프로그램 키를 저장해야 합니다. 다음을 수행합니다.
1. Azure AD B2C 테넌트로 이동하고 **B2C 설정** > **ID 경험 프레임워크** > **정책 키**를 차례로 선택합니다.
1. **+추가**를 선택합니다.
1. 다음 옵션을 선택하거나 입력합니다.
   * **수동**을 선택합니다.
   * **이름**에 대해 Azure AD 테넌트 이름과 일치하는 이름(예: `ContosoAppSecret`)을 선택합니다.  `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
   * **비밀** 상자에서 응용 프로그램 키를 붙여넣습니다.
   * **서명**을 선택합니다.
1. **만들기**를 선택합니다.
1. `B2C_1A_ContosoAppSecret` 키를 만들었는지 확인합니다.


## <a name="add-a-claims-provider-in-your-base-policy"></a>기본 정책에서 클레임 공급자 추가

사용자가 Azure AD를 사용하여 로그인하도록 하려면 Azure AD를 클레임 공급자로 정의해야 합니다. 즉, Azure AD B2C가 통신하는 끝점을 지정해야 합니다. 끝점은 Azure AD B2C에서 사용하는 일단의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다. 

정책의 확장 파일에서 `<ClaimsProvider>` 노드에 Azure AD를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. 작업 디렉터리에서 확장 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. `<ClaimsProviders>` 섹션을 찾습니다. 존재하지 않는 경우 루트 노드에 추가합니다.
1. 다음과 같이 새로운 `<ClaimsProvider>` 노드를 추가합니다.

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
                </OutputClaims>
                <OutputClaimsTransformations>
                    <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                    <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                    <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                    <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
                </OutputClaimsTransformations>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. `<ClaimsProvider>` 노드 아래에서 `<Domain>` 값을 다른 ID 공급자와 구분하는 데 사용할 수 있는 고유한 값으로 업데이트합니다.
1. `<ClaimsProvider>` 노드 아래에서 `<DisplayName>` 값을 클레임 공급자에게 친숙한 이름으로 업데이트합니다. 이 값은 현재 사용되지 않습니다.

### <a name="update-the-technical-profile"></a>기술 프로필 업데이트

Azure AD 끝점에서 토큰을 가져오려면 Azure AD B2C에서 Azure AD와 통신하는 데 사용해야 하는 프로토콜을 정의해야 합니다. 이 작업은 `<ClaimsProvider>`의 `<TechnicalProfile>` 요소 내에서 수행됩니다.
1. `<TechnicalProfile>` 노드의 ID를 업데이트합니다. 이 ID는 정책의 다른 부분에서 이 기술 프로필을 참조하는 데 사용합니다.
1. `<DisplayName>` 값을 업데이트합니다. 이 값은 로그인 화면의 로그인 단추에 표시됩니다.
1. `<Description>` 값을 업데이트합니다.
1. Azure AD는 OpenID Connect 프로토콜을 사용하므로 `<Protocol>` 값이 `"OpenIdConnect"`인지 확인합니다.

앞에서 언급한 XML의 `<Metadata>` 섹션을 업데이트하여 특정 Azure AD 테넌트의 구성 설정을 반영해야 합니다. XML 파일에서 다음과 같이 메타데이터 값을 업데이트합니다.

1. `<Item Key="METADATA">`를 `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`으로 설정합니다. 여기서 `yourAzureADtenant`는 Azure AD 테넌트 이름(예: contoso.com)입니다.
1. 브라우저를 열고 방금 업데이트한 `METADATA` URL로 이동합니다.
1. 브라우저에서 '발급자' 개체를 찾아 해당 값을 복사합니다. `https://sts.windows.net/{tenantId}/`과 같아야 합니다.
1. XML 파일에서 `<Item Key="ProviderName">` 값을 붙여넣습니다.
1. 앱 등록에서 `<Item Key="client_id">`를 응용 프로그램 ID로 설정합니다.
1. 앱 등록에서 `<Item Key="IdTokenAudience">`를 응용 프로그램 ID로 설정합니다.
1. `<Item Key="response_types">`을 `id_token`로 설정했는지 확인합니다.
1. `<Item Key="UsePolicyInRedirectUri">`을 `false`로 설정했는지 확인합니다.

또한 Azure AD B2C 테넌트에 등록한 Azure AD 비밀을 Azure AD `<ClaimsProvider>` 정보에 연결해야 합니다.

* 앞에서 언급한 XML 파일의 `<CryptographicKeys>` 섹션에서 `StorageReferenceId` 값을 정의한 비밀의 참조 ID(예: `ContosoAppSecret`)로 업데이트합니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

지금까지 Azure AD B2C에서 Azure AD 디렉터리와 통신하는 방법을 알 수 있도록 정책을 구성했습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다. 이렇게 하려면 다음을 수행합니다.

1. Azure AD B2C 테넌트에서 **모든 정책** 블레이드를 엽니다.
1. **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.
1. 확장 파일(TrustFrameworkExtensions.xml)을 업로드하고 유효성 검사가 실패하지 않았는지 확인합니다.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>사용자 경험에 Azure AD 클레임 공급자 등록

이제 사용자 경험 중 하나에 Azure AD ID 공급자를 추가해야 합니다. 이 시점에서 ID 공급자가 설정되었지만 등록/로그인 화면에서 사용할 수는 없습니다. 사용할 수 있게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Azure AD ID 공급자도 포함하도록 수정합니다.

1. 정책의 기본 파일(예: TrustFrameworkBase.xml)을 엽니다.
1. `<UserJourneys>` 요소를 찾고 `Id="SignUpOrSignIn"`을 포함한 `<UserJourney>` 노드 전체를 복사합니다.
1. 확장 파일(예: TrustFrameworkExtensions.xml)을 열고 `<UserJourneys>` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 복사한 `<UserJourney>` 노드 전체를 `<UserJourneys>` 요소의 자식으로 붙여넣습니다.
1. 새 사용자 경험의 ID 이름을 바꿉니다(예: `SignUpOrSignUsingContoso`로 이름 바꾸기).

### <a name="display-the-button"></a>"단추" 표시

`<ClaimsProviderSelection>` 요소는 등록/로그인 화면에서 ID 공급자 단추를 사용하는 것과 유사합니다. Azure AD에 `<ClaimsProviderSelection>` 요소를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다. 이 요소를 추가하려면 다음을 수행합니다.

1. 방금 만든 사용자 경험에서 `Order="1"`이 포함된 `<OrchestrationStep>` 노드를 찾습니다.
1. 다음을 추가합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. `TargetClaimsExchangeId`을 적절한 값으로 설정합니다. 다른 값과 동일한 규칙인 *\[ClaimProviderName\]Exchange*를 따르는 것이 좋습니다.

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 Azure AD와 통신하여 토큰을 수신할 수 있게 됩니다. Azure AD 클레임 공급자의 기술 프로필을 연결하여 동작에 단추를 연결합니다.

1. `<UserJourney>` 노드에 `Order="2"`가 포함된 `<OrchestrationStep>`을 찾습니다.
1. 다음을 추가합니다.

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. `Id`를 이전 섹션의 `TargetClaimsExchangeId`와 동일한 값으로 업데이트합니다.
1. `TechnicalProfileReferenceId`를 이전에 만든 기술 프로필의 ID(예: ContosoProfile)로 업데이트합니다.

### <a name="upload-the-updated-extension-file"></a>업데이트된 확장 파일 업로드

확장 파일을 수정하는 작업을 완료했습니다. 저장합니다. 그런 다음 파일을 업로드하고 모든 유효성 검사가 성공했는지 확인합니다.

### <a name="update-the-rp-file"></a>RP 파일 업데이트

이제 방금 만든 사용자 경험을 시작할 RP(신뢰 당사자) 파일을 업데이트해야 합니다.

1. 작업 디렉터리에 SignUpOrSignIn.xml의 복사본을 만들고 이름을 바꿉니다(예: SignUpOrSignInWithAAD.xml로 이름 바꾸기).
1. 새 파일을 열고 `<TrustFrameworkPolicy>`의 `PolicyId` 특성을 고유한 값(예: SignUpOrSignInWithAAD)으로 업데이트합니다. <br> 이는 정책의 이름(예: B2C\_1A\_SignUpOrSignInWithAAD)입니다.
1. 만든 새 사용자 경험의 ID(SignUpOrSignUsingContoso)와 일치하도록 `<DefaultUserJourney>`의 `ReferenceId` 특성을 수정합니다.
1. 변경 내용을 저장하고 파일을 업로드합니다.

## <a name="troubleshooting"></a>문제 해결

해당 블레이드를 열고 **지금 실행**을 클릭하여 방금 업로드한 사용자 지정 정책을 테스트합니다. 문제를 진단하려면 [문제 해결](active-directory-b2c-troubleshoot-custom.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com)에 대한 피드백을 제공합니다.
