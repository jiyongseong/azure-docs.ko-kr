---
title: 사용자 지정 정책을 사용하여 다중 테넌트 Azure AD ID 공급자 추가 - Azure Active Directory B2C | Microsoft Docs
description: 사용자 지정 정책을 사용하여 다중 테넌트 Azure AD ID 공급자 추가 - Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: alexsi
editor: parakhj
ms.assetid: 33c64001-5261-4ed9-8f46-b09839165250
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/14/2018
ms.author: parakhj
ms.openlocfilehash: d5e5ab1262a9d33fcf34cce91113f39c8c8936f4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200521"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책을 사용하여 사용자가 다중 테넌트 Azure AD ID 공급자에 로그인할 수 있게 함

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 특정 Azure AD(Azure Active Directory)에 대해 다중 테넌트 엔드포인트를 사용하여 사용자 로그인을 활성화하는 방법에 대해 설명합니다. 이렇게 하면 각 테넌트에 대한 기술 공급자를 구성하지 않아도 여러 Azure AD 테넌트의 사용자가 Azure AD B2C에 로그인할 수 있습니다. 그러나 이러한 테넌트의 게스트 멤버는 로그인할 수 **없습니다**. 게스트 멤버가 로그인하려면 [각 테넌트를 개별적으로 구성](active-directory-b2c-setup-aad-custom.md)해야 합니다.

>[!NOTE]
> 조직의 Azure AD 테넌트로 "contoso.com"을 사용하고, 다음 지침에서는 "fabrikamb2c.onmicrosoft.com"을 Azure AD B2C 테넌트로 사용합니다.

## <a name="prerequisites"></a>필수 조건

[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md) 문서의 단계를 완료합니다.

이러한 단계는 다음과 같습니다.
     
1. Azure AD B2C(Azure Active Directory B2C) 테넌트 만들기
1. Azure AD B2C 응용 프로그램 만들기    
1. 두 개의 정책 엔진 응용 프로그램 등록  
1. 키 설정 
1. 시작 팩 설정

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>1단계. 다중 테넌트 Azure AD 앱 만들기

다중 테넌트 Azure AD 엔드포인트를 사용하여 사용자에 대한 로그인을 활성화하려면 다중 테넌트 응용 프로그램을 Azure AD 테넌트 중 하나에 등록해야 합니다. 이 문서에서는 Azure AD B2C 테넌트에 다중 테넌트 Azure AD 응용 프로그램을 만드는 방법을 알아봅니다. 그런 다음, 다중 테넌트 Azure AD 응용 프로그램을 사용하여 사용자에 대해 로그인을 활성화합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 위쪽 막대에서 계정을 선택합니다. **디렉터리** 목록에서 Azure AD B2C 테넌트를 선택하여 Azure AD 응용 프로그램(fabrikamb2c.onmicrosoft.com)을 등록합니다.
1. 왼쪽 창에서 **더 많은 서비스**를 클릭하고 "앱 등록"을 검색합니다.
1. **새 응용 프로그램 등록**을 선택합니다.
1. 응용 프로그램의 이름(예: `Azure AD B2C App`)을 입력합니다.
1. 응용 프로그램 종류에 대해 **웹앱/API**를 선택합니다.
1. **로그온 URL**에 대해 다음 URL을 입력합니다. 여기서 `yourtenant`는 Azure AD B2C 테넌트의 이름(`fabrikamb2c.onmicrosoft.com`)으로 바뀝니다.

    >[!NOTE]
    >"yourtenant"의 값은 **로그온 URL**에서 모두 소문자여야 합니다.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. 응용 프로그램 ID를 저장합니다.
1. 새로 만든 응용 프로그램을 선택합니다.
1. **설정** 블레이드에서 **속성**을 선택합니다.
1. **다중 테넌트**를 **예**로 설정합니다.
1. **설정** 블레이드 아래에서 **키**를 선택합니다.
1. 새 키를 만들고 저장합니다. 이 키는 다음 섹션의 단계에서 사용합니다.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>2단계. Azure AD B2C에 Azure AD 키 추가

Azure AD B2C 설정에서 응용 프로그램 키를 등록해야 합니다. 다음을 수행합니다.

1. Azure AD B2C에 대한 설정 메뉴로 이동합니다.
1. **ID 환경 프레임워크** > **정책 키**를 클릭합니다.
1. **+추가**를 선택합니다.
1. 다음 옵션을 선택하거나 입력합니다.
   * **수동**을 선택합니다.
   * **이름**에 대해 Azure AD 테넌트 이름과 일치하는 이름(예: `AADAppSecret`)을 선택합니다.  `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
   * **비밀** 상자에서 응용 프로그램 키를 붙여넣습니다.
   * **서명**을 선택합니다.
1. **만들기**를 선택합니다.
1. `B2C_1A_AADAppSecret` 키를 만들었는지 확인합니다.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>3단계. 기본 정책에서 클레임 공급자 추가

사용자가 Azure AD를 사용하여 로그인하도록 하려면 Azure AD를 클레임 공급자로 정의해야 합니다. 즉, Azure AD B2C가 통신하는 끝점을 지정해야 합니다. 끝점은 Azure AD B2C에서 사용하는 일단의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다. 

정책의 확장 파일에서 `<ClaimsProvider>` 노드에 Azure AD를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. 작업 디렉터리에서 확장 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. `<ClaimsProviders>` 섹션을 찾습니다. 존재하지 않는 경우 루트 노드에 추가합니다.
1. 다음과 같이 새로운 `<ClaimsProvider>` 노드를 추가합니다.

```XML
<ClaimsProvider>
  <Domain>commonaad</Domain>
  <DisplayName>Common AAD</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Common-AAD">
      <DisplayName>Multi-Tenant AAD</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

        <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->

      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. `<ClaimsProvider>` 노드 아래에서 `<Domain>` 값을 다른 ID 공급자와 구분하는 데 사용할 수 있는 고유한 값으로 업데이트합니다.
1. `<TechnicalProfile>` 노드 아래에서 `<DisplayName>` 값을 업데이트합니다. 이 값은 로그인 화면의 로그인 단추에 표시됩니다.
1. `<Description>` 값을 업데이트합니다.
1. Azure AD 다중 테넌트 앱 등록에서 `<Item Key="client_id">`를 응용 프로그램 ID로 설정합니다.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>3.1단계 Azure AD 테넌트의 특정 목록에 대한 액세스 제한

> [!NOTE]
> **ValidTokenIssuerPrefixes**의 값으로 `https://sts.windows.net`을 사용하면 모든 Azure AD 사용자가 앱에 로그인할 수 있습니다.

유효한 토큰 발급자 목록을 업데이트하고 사용자가 로그인할 수 있는 특정 Azure AD 테넌트 목록에 대한 액세스를 제한해야 합니다. 값을 구하려면 사용자가 로그인하게 하려는 특정 Azure AD 테넌트 각각에 대한 메타데이터를 살펴야 합니다. 데이터의 형식은 `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`과 유사하며 여기서 `yourAzureADtenant`는 Azure AD 테넌트 이름(contoso.com 또는 다른 Azure AD 테넌트)입니다.
1. 브라우저를 열고 메타데이터 URL로 이동합니다.
1. 브라우저에서 '발급자' 개체를 찾아 해당 값을 복사합니다. `https://sts.windows.net/{tenantId}/`과 같아야 합니다.
1. `ValidTokenIssuerPrefixes` 키 값을 붙여 넣습니다. 여러 값은 쉼표를 사용하여 구분해 추가할 수 있습니다. 위의 샘플 XML의 주석 처리를 예로 들 수 있습니다.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>4단계. Azure AD 계정 클레임 공급자 등록

### <a name="step-41-make-a-copy-of-the-user-journey"></a>4.1단계 사용자 경험의 복사본 만들기

이제 사용자 경험 중 하나에 Azure AD ID 공급자를 추가해야 합니다. 이 시점에서 ID 공급자가 설정되었지만 등록/로그인 화면에서 사용할 수는 없습니다.

사용할 수 있게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Azure AD ID 공급자도 포함하도록 수정합니다.

1. 정책의 기본 파일(예: TrustFrameworkBase.xml)을 엽니다.
1. `<UserJourneys>` 요소를 찾고 `Id="SignUpOrSignIn"`을 포함한 `<UserJourney>` 노드 전체를 복사합니다.
1. 확장 파일(예: TrustFrameworkExtensions.xml)을 열고 `<UserJourneys>` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 복사한 `<UserJourney>` 노드 전체를 `<UserJourneys>` 요소의 자식으로 붙여넣습니다.
1. 새 사용자 경험의 ID 이름을 바꿉니다(예: `SignUpOrSignUsingAzureAD`로 이름 바꾸기). 

### <a name="step-42-display-the-button"></a>4.2단계 "단추" 표시

`<ClaimsProviderSelection>` 요소는 등록/로그인 화면에서 ID 공급자 단추를 사용하는 것과 유사합니다. Azure AD에 `<ClaimsProviderSelection>` 요소를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다. 이 요소를 추가하려면 다음을 수행합니다.

1. 만든 사용자 경험에서 `Order="1"`이 포함된 `<OrchestrationStep>` 노드를 찾습니다.
1. 다음을 추가합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. `TargetClaimsExchangeId`을 적절한 값으로 설정합니다. 다른 값과 동일한 규칙인 *\[ClaimProviderName\]Exchange*를 따르는 것이 좋습니다.

### <a name="step-43-link-the-button-to-an-action"></a>4.3단계 작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 Azure AD와 통신하여 토큰을 수신할 수 있게 됩니다. Azure AD 클레임 공급자의 기술 프로필을 연결하여 동작에 단추를 연결합니다.

1. `<UserJourney>` 노드에 `Order="2"`가 포함된 `<OrchestrationStep>`을 찾습니다.
1. 다음을 추가합니다.

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. `Id`를 이전 섹션의 `TargetClaimsExchangeId`와 동일한 값으로 업데이트합니다.
1. `TechnicalProfileReferenceId`를 이전에 만든 기술 프로필의 ID(Common-AAD)로 업데이트합니다.

## <a name="step-5-create-a-new-rp-policy"></a>5단계: 새 RP 정책 만들기

이제 방금 만든 사용자 경험을 시작할 RP(신뢰 당사자) 파일을 업데이트해야 합니다.
 
1. 작업 디렉터리에 SignUpOrSignIn.xml의 복사본을 만들고 이름을 바꿉니다(예: SignUpOrSignInWithAAD.xml로 이름 바꾸기).  
1. 새 파일을 열고 `<TrustFrameworkPolicy>`의 `PolicyId` 특성을 고유한 값(예: SignUpOrSignInWithAAD)으로 업데이트합니다. 이는 정책의 이름(예: B2C\_1A\_SignUpOrSignInWithAAD)입니다. 
1. 만든 새 사용자 경험의 ID(SignUpOrSignUsingAzureAD)와 일치하도록 `<DefaultUserJourney>`의 `ReferenceId` 특성을 수정합니다. 
1. 변경 내용을 저장하고 파일을 업로드합니다. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>6단계: 테넌트에 정책 업로드

1. [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트](active-directory-b2c-navigate-to-b2c-context.md)로 전환한 다음 **Azure AD B2C**를 선택합니다.
1. **ID 경험 프레임워크**를 선택합니다.
1. **모든 정책**을 선택합니다.
1. **정책 업로드**를 선택합니다.
1. **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.
1. `TrustFrameworkExtensions.xml` 파일과 RP 파일(예: `SignUpOrSignInWithAAD.xml`)을 업로드하고 유효성 검사에 통과하는지 확인합니다.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>7단계: 지금 실행을 사용하여 사용자 지정 정책 테스트

1. **Azure AD B2C 설정**을 선택한 다음 **ID 경험 프레임워크**를 선택합니다.
    > [!NOTE]
    > 지금 실행을 사용하려면 하나 이상의 응용 프로그램이 테넌트에 미리 등록되어 있어야 합니다. 응용 프로그램을 등록하는 방법은 Azure AD B2C [시작](active-directory-b2c-get-started.md) 문서 또는 [응용 프로그램 등록](active-directory-b2c-app-registration.md) 문서를 참조하세요.

1. 업로드한 RP(신뢰 당사자) 사용자 지정 정책(*B2C\_1A\_SignUpOrSignInWithAAD*)을 연 다음, **지금 실행**을 선택합니다.
1. 이제 Azure AD 계정을 사용하여 로그인할 수 있어야 합니다.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>8단계: (선택 사항) 프로필 편집 사용자 경험에 Azure AD 계정 클레임 공급자 등록

`ProfileEdit` 사용자 경험에 Azure AD 계정 ID 공급자를 추가하려고 할 수도 있습니다. 사용자 경험을 사용할 수 있도록 하려면 4~6단계를 반복합니다. 이번에는 `Id="ProfileEdit"`를 포함하는 `<UserJourney>` 노드를 선택합니다. 정책을 저장하고, 업로드하고, 테스트합니다.

## <a name="troubleshooting"></a>문제 해결

문제를 진단하려면 [문제 해결](active-directory-b2c-troubleshoot-custom.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[AADB2CPreview@microsoft.com](mailto:AADB2CPreview@microsoft.com)에 대한 피드백을 제공합니다.
