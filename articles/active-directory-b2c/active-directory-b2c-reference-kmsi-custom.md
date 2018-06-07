---
title: 'Azure Active Directory B2C: KMSI| Microsoft Docs'
description: "'로그인 상태 유지'를 설정하는 방법을 보여주는 항목"
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/05/2016
ms.author: davidmu
ms.openlocfilehash: 073ba8eef7f2f42d1c308fb20d3bfdbfc8d321b7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C: 'KMSI(로그인 유지)' 활성화  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이제 Azure AD B2C를 사용하면 웹 및 네이티브 응용 프로그램이 'KMSI(로그인 유지)' 기능을 사용할 수 있습니다. 이 기능은 사용자 이름과 암호를 다시 입력하라는 메시지 없이 사용자를 응용 프로그램에 반환하는 액세스 권한을 부여합니다. 이 액세스 권한은 사용자가 로그아웃할 때 해지됩니다. 

공용 컴퓨터에서 이 옵션을 선택하지 않는 것이 좋습니다. 

![이미지](images/kmsi.PNG)


## <a name="prerequisites"></a>필수 조건

[시작](active-directory-b2c-get-started-custom.md)에서 설명한 대로 로컬 계정 등록/로그인을 허용하도록 구성된 Azure AD B2C 테넌트

## <a name="how-to-enable-kmsi"></a>KMSI를 사용하는 방법

트러스트 프레임워크 확장 정책을 다음과 같이 변경합니다.

## <a name="adding-a-content-definition-element"></a>콘텐츠 정의 요소 추가 

확장 파일의 `BuildingBlocks` 노드는 `ContentDefinitions` 요소를 포함해야 합니다. 

1. `ContentDefinitions` 섹션에서 `api.signuporsigninwithkmsi` ID를 사용하여 새로운 `ContentDefinition`을 정의합니다.
2. 새로운 `ContentDefinition`에는 다음과 같이 `LoadUri`, `RecoveryUri` 및 `DataUri`가 포함되어야 합니다.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0`는 로그인 페이지에서 KMSI 확인란을 열 수 있는 컴퓨터 이해 가능 식별자입니다. 이 값을 변경하지 않았는지 확인하세요. 

```XML
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.signuporsigninwithkmsi">
        <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Signin and Signup</Item>
        </Metadata>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>                       
```



## <a name="add-a--local-account-sign-in-claims-provider"></a>로컬 계정 로그인 클레임 공급자 추가 

로컬 계정 로그인을 정책의 확장 파일에서 `<ClaimsProvider>` 노드에 대한 클레임 공급자로 정의할 수 있습니다.

1. 작업 디렉터리에서 확장 파일(TrustFrameworkExtensions.xml)을 엽니다. 
2. `<ClaimsProviders>` 섹션을 찾습니다. 존재하지 않는 경우 루트 노드에 추가합니다.
3. [시작](active-directory-b2c-get-started-custom.md)의 시작 팩은 '로컬 계정 로그인' 클레임 공급자와 함께 제공됩니다. 
4. 그렇지 않은 경우 다음과 같이 새로운 `<ClaimsProvider>` 노드를 추가합니다.

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="login-NonInteractive">
           <Metadata>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
           </Metadata>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
 </ClaimsProviders>
```

### <a name="add-the-application-ids-to-your-custom-policy"></a>사용자 지정 정책에 응용 프로그램 ID 추가

확장 파일(`TrustFrameworkExtensions.xml`)에 응용 프로그램 ID를 추가합니다.

1. 확장 파일(TrustFrameworkExtensions.xml)에서 `<TechnicalProfile Id="login-NonInteractive">` 및 `<TechnicalProfile Id="login-NonInteractive-PasswordChange">` 요소를 찾습니다.

2. `IdentityExperienceFrameworkAppId`의 모든 인스턴스를 [시작](active-directory-b2c-get-started-custom.md)에서 설명한 대로 ID 환경 프레임워크의 응용 프로그램 ID로 바꿉니다. 다음은 예제입니다.

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. `ProxyIdentityExperienceFrameworkAppId`의 모든 인스턴스를 [시작](active-directory-b2c-get-started-custom.md)에서 설명한 대로 프록시 ID 환경 프레임워크의 응용 프로그램 ID로 바꿉니다.

4. 확장 파일을 저장합니다.

## <a name="create-a-kmsi-in-enabled-user-journey"></a>활성화된 사용자 경험에서 KMSI 만들기

이제 사용자 경험에 로컬 계정 로그인 클레임 공급자를 추가해야 합니다. 

1. 정책의 기본 파일(예: TrustFrameworkBase.xml)을 엽니다.
2. `<UserJourneys>` 요소를 찾고 `Id="SignUpOrSignIn"`을 포함한 `<UserJourney>` 노드 전체를 복사합니다.
3. 확장 파일(예: TrustFrameworkExtensions.xml)을 열고 `<UserJourneys>` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4. 복사한 `<UserJourney>` 노드 전체를 `<UserJourneys>` 요소의 자식으로 붙여넣습니다.
5. 새 사용자 경험의 ID 이름을 바꿉니다(예: `SignUpOrSignInWithKmsi`로 이름 바꾸기).
6. 마지막으로 이전 단계에서 정의한 대로 `OrchestrationStep 1`에서 `ContentDefinitionReferenceId`를 `api.signuporsigninwithkmsi`로 변경합니다. 이를 통해 사용자 경험에서 확인란을 활성화합니다. 
7. 확장 파일을 수정하는 작업을 완료했습니다. 이 파일을 저장하고 업로드합니다. 모든 유효성 검사에 성공했는지 확인합니다.

```XML
<UserJourneys>
    <UserJourney Id="SignUpOrSignInWithKmsi">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- This step reads any user attributes that we may not have received when in the token. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

## <a name="create-a-relying-party-rp-file"></a>RP(신뢰 당사자) 파일을 만듭니다.

다음으로, 만든 사용자 경험을 시작할 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 SignUpOrSignIn.xml의 복사본을 만듭니다. 그런 다음 이름을 바꿉니다(예: SignUpOrSignInWithKmsi.xml).

2. 새 파일을 열고 `<TrustFrameworkPolicy>`의 `PolicyId` 특성을 고유한 값으로 업데이트합니다. 이는 정책의 이름(예: SignUpOrSignInWithKmsi)입니다.

3. `<DefaultUserJourney>`에서 `ReferenceId` 특성을 수정하여 만든 새 사용자 경험의 `Id`와 일치시킵니다(예: SignUpOrSignInWithKmsi).

4. KMSI는 `UserJourneyBehaviors`에서 구성됩니다. 

5. **`KeepAliveInDays`**는 사용자가 로그인한 상태로 유지되는 기간을 제어합니다. 다음 예제에서 KMSI 세션은 사용자가 자동 인증을 수행하는 빈도에 관계 없이 14일 후에 자동으로 만료됩니다.

   `KeepAliveInDays` 값을 0으로 설정하면 KMSI 기능을 해제합니다. 기본적으로 이 값은 0입니다.

6. **`SessionExpiryType`**은 *롤링*인 경우 KMSI 세션은 사용자가 자동 인증을 수행할 때마다 14일이 늘어납니다.  *롤링*은 선택한 경우 날짜를 최소로 유지하는 것이 좋습니다. 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
         <SessionExpiryType>Absolute</SessionExpiryType>
         <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
       </UserJourneyBehaviors>
       <TechnicalProfile Id="PolicyProfile">
         <DisplayName>PolicyProfile</DisplayName>
         <Protocol Name="OpenIdConnect" />
         <OutputClaims>
           <OutputClaim ClaimTypeReferenceId="displayName" />
           <OutputClaim ClaimTypeReferenceId="givenName" />
           <OutputClaim ClaimTypeReferenceId="surname" />
           <OutputClaim ClaimTypeReferenceId="email" />
           <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
         </OutputClaims>
         <SubjectNamingInfo ClaimType="sub" />
       </TechnicalProfile>
       </RelyingParty>

7. 변경 내용을 저장하고 파일을 업로드합니다.

8. Azure Portal에서 업로드한 사용자 지정 정책을 테스트하려면 정책 블레이드로 이동하고 **지금 실행**을 클릭합니다.


## <a name="link-to-sample-policy"></a>샘플 정책에 연결

[여기](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)에서 샘플 정책을 찾을 수 있습니다.








