---
title: 'Azure Active Directory B2C: 사용자 지정 정책을 사용하여 OAuth1 ID 공급자로 Twitter 추가'
description: OAuth1 프로토콜을 사용하여 ID 공급자로 Twitter 사용
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/23/2017
ms.author: davidmu
ms.openlocfilehash: 4a7cc552f49877874dc173a47efe22873d5aeeed
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책을 사용하여 OAuth1 ID 공급자로 Twitter 추가
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 Twitter 계정의 사용자가 로그인할 수 있도록 하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md) 문서의 단계를 완료합니다.

## <a name="step-1-create-a-twitter-account-application"></a>1단계: Twitter 계정 응용 프로그램 만들기
Azure AD B2C(Azure Active Directory B2C)에서 Twitter를 ID 공급자로 사용하려면 Twitter 응용 프로그램을 만들고 올바른 매개 변수를 제공해야 합니다. [Twitter 등록 페이지](https://twitter.com/signup)로 이동하여 Twitter 응용 프로그램을 등록할 수 있습니다.

1. [Twitter 개발자](https://apps.twitter.com/) 웹 사이트로 이동하고 Twitter 계정 자격 증명을 사용하여 로그인하고 **새 앱 만들기**를 선택합니다.

    ![Twitter 계정 - 새 앱 만들기](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. **응용 프로그램 만들기** 창에서 다음을 수행합니다.
 
    a. 새 앱에 대한 **이름** 및 **설명**을 입력합니다. 

    나. **웹 사이트** 상자에 **https://login.microsoftonline.com**을 붙여넣습니다. 

    다. **콜백 URL** 상자에 **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**를 붙여넣습니다. {*tenant*}를 사용자의 테넌트 이름(예: contosob2c.onmicrosoft.com)으로 바꿉니다. HTTPS 체계를 사용 중인지 확인합니다. 

    d. 페이지 아래쪽에 있는 약관을 읽고 동의한 후 **Twitter 응용 프로그램 만들기**를 선택합니다.

    ![Twitter 계정 - 새 앱 추가](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. **B2C 데모** 창에서 **설정**을 선택하고, **이 응용 프로그램을 Twitter를 사용하여 로그인하는 데 사용하도록 허용** 확인란을 선택한 다음 **업데이트 설정**을 선택합니다.

4. **키 및 액세스 토큰**을 선택하고, **소비자 키(API 키)** 및 **소비자 비밀(API 비밀)** 값을 적어둡니다.

    ![Twitter 계정 - 응용 프로그램 속성 설정](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >소비자 암호는 중요한 보안 자격 증명입니다. 다른 사람과 이 암호를 공유하거나 앱과 함께 배포하지 마세요.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>2단계: Azure AD B2C에 Twitter 계정 응용 프로그램 키 추가
Twitter 계정으로 페더레이션하려면 응용 프로그램 대신 Azure AD B2C를 신뢰하기 위해 Twitter 계정에 대한 소비자 비밀이 필요합니다. Azure AD B2C 테넌트에 Twitter 응용 프로그램의 소비자 비밀을 저장하려면 다음을 수행합니다. 

1. Azure AD B2C 테넌트에서 **B2C 설정** > **ID 경험 프레임워크**를 차례로 선택합니다.

2. 테넌트에 사용 가능한 키를 보려면 **정책 키**를 선택합니다.

3. **추가**를 선택합니다.

4. **옵션** 상자에서 **수동**을 선택합니다.

5. **이름** 상자에서 **TwitterSecret**을 선택합니다.  
    *B2C_1A_* 접두사를 자동으로 추가할 수 있습니다.

6. **비밀** 상자에 [응용 프로그램 등록 포털](https://apps.dev.microsoft.com)의 Microsoft 응용 프로그램 비밀을 입력합니다.

7. **키 사용**에는 **암호화**를 사용합니다.

8. **만들기**를 선택합니다.

9. `B2C_1A_TwitterSecret` 키를 만들었는지 확인합니다.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>3단계: 확장 정책에서 클레임 공급자 추가

사용자가 Twitter 계정을 사용하여 로그인하도록 하려면 Twitter를 클레임 공급자로 정의해야 합니다. 즉, Azure AD B2C가 통신하는 끝점을 지정해야 합니다. 끝점은 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

확장 정책 파일에서 `<ClaimsProvider>` 노드를 추가하여 Twitter를 클레임 공급자로 정의합니다.

1. 작업 디렉터리에서 *TrustFrameworkExtensions.xml* 확장 정책 파일을 엽니다. 

2. `<ClaimsProviders>` 섹션을 검색합니다.

3. `<ClaimsProviders>` 노드에서 다음 XML 코드 조각을 추가합니다.  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. *client_id*` 값을 Twitter 계정 응용 프로그램 소비자 키로 바꿉니다.

5. 파일을 저장합니다.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>4단계: 등록 또는 로그인 사용자 경험에 Twitter 계정 클레임 공급자 등록
ID 공급자를 설정했습니다. 그러나 등록 또는 로그인 창에서 아직 사용할 수 없습니다. 이제 `SignUpOrSignIn` 사용자 경험에 Twitter 계정 ID 공급자를 추가해야 합니다.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>4.1단계: 사용자 경험의 복사본 만들기
사용자 경험을 사용할 수 있도록 하려면 기존 사용자 경험 템플릿의 복제본을 만든 다음 Twitter ID 공급자를 추가합니다.

>[!NOTE]
>`<UserJourneys>` 요소를 정책의 기본 파일에서 *TrustFrameworkExtensions.xml* 확장 파일로 복사한 경우 다음 섹션으로 건너뛸 수 있습니다.

1. 정책의 기본 파일(예: TrustFrameworkBase.xml)을 엽니다.

2. `<UserJourneys>` 요소를 검색하고, `<UserJourney>` 노드의 전체 내용을 선택한 다음 **잘라내기**를 선택하여 선택한 텍스트를 클립보드로 이동합니다.

3. 확장 파일(예: TrustFrameworkExtensions.xml)을 연 다음 `<UserJourneys>` 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.

4. 2단계에서 클립보드로 이동한 `<UserJourney>` 노드의 전체 내용을 `<UserJourneys>` 요소로 붙여넣습니다.

### <a name="step-42-display-the-button"></a>4.2단계: "단추" 표시
`<ClaimsProviderSelections>` 요소는 클레임 공급자 선택 옵션 목록과 해당 순서를 정의합니다. `<ClaimsProviderSelection>` 노드는 등록 또는 로그인 페이지에서 ID 공급자 단추를 사용하는 것과 유사합니다. Twitter 계정에 `<ClaimsProviderSelection>` 노드를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다. 이 요소를 추가하려면 다음을 수행합니다.

1. 복사한 사용자 경험에서 `Id="SignUpOrSignIn"`을 포함하는 `<UserJourney>` 노드를 검색합니다.

2. `Order="1"`을 포함하는 `<OrchestrationStep>` 노드를 찾습니다.

3. `<ClaimsProviderSelections>` 요소에서 다음 XML 코드 조각을 추가합니다.

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>4.3단계: 작업에 단추 연결
이제 단추를 만들었으므로 작업에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 Twitter 계정과 통신하여 토큰을 수신할 수 있게 됩니다. Twitter 계정 클레임 공급자의 기술 프로필을 연결하여 작업에 단추를 연결합니다.

1. `<UserJourney>` 노드에서 `Order="2"`를 포함하는 `<OrchestrationStep>` 노드를 검색합니다.
2. `<ClaimsExchanges>` 요소에서 다음 XML 코드 조각을 추가합니다.

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* `Id`에 이전 섹션의 `TargetClaimsExchangeId`와 동일한 값이 있는지 확인합니다.
    >* `TechnicalProfileReferenceId` ID를 이전에 만든 기술 프로필(Twitter-OAUTH1)로 설정하도록 합니다.

## <a name="step-5-upload-the-policy-to-your-tenant"></a>5단계: 테넌트에 정책 업로드
1. [Azure Portal](https://portal.azure.com)에서 [Azure AD B2C 테넌트의 컨텍스트](active-directory-b2c-navigate-to-b2c-context.md)로 전환한 다음 **Azure AD B2C**를 선택합니다.

2. **ID 경험 프레임워크**를 선택합니다.

3. **모든 정책**을 선택합니다.

4. **정책 업로드**를 선택합니다.

5. **정책이 있는 경우 덮어쓰기** 확인란을 선택합니다.

6. *TrustFrameworkBase.xml* 및 *TrustFrameworkExtensions.xml* 파일을 업로드하고, 유효성 검사를 통과했는지 확인합니다.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>6단계: 지금 실행을 사용하여 사용자 지정 정책 테스트

1. **Azure AD B2C 설정**을 선택한 다음 **ID 경험 프레임워크**를 선택합니다.

    >[!NOTE]
    >지금 실행을 사용하려면 하나 이상의 응용 프로그램이 테넌트에 미리 등록되어 있어야 합니다. 응용 프로그램을 등록하는 방법은 Azure AD B2C [시작](active-directory-b2c-get-started.md) 문서 또는 [응용 프로그램 등록](active-directory-b2c-app-registration.md) 문서를 참조하세요.

2. 업로드한 RP(신뢰 당사자) 사용자 지정 정책인 **B2C_1A_signup_signin**을 연 다음 **지금 실행**을 선택합니다.  
    이제 Twitter 계정을 사용하여 로그인할 수 있어야 합니다.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>7단계: (선택 사항) 프로필 편집 사용자 경험에 Twitter 계정 클레임 공급자 등록
`ProfileEdit` 사용자 경험에 Twitter 계정 ID 공급자를 추가하려고 할 수도 있습니다. 사용자 경험을 사용할 수 있도록 하려면 "4단계"를 반복합니다. 이번에는 `Id="ProfileEdit"`를 포함하는 `<UserJourney>` 노드를 선택합니다. 정책을 저장하고, 업로드하고, 테스트합니다.


## <a name="optional-download-the-complete-policy-files"></a>(선택 사항) 전체 정책 파일 다운로드
[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md) 연습을 완료한 후에 고유한 사용자 지정 정책 파일을 사용하여 시나리오를 빌드하는 것이 좋습니다. 참조를 위한 [샘플 정책 파일](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app)이 제공됩니다.
