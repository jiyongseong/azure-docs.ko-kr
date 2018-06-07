---
title: 'Azure Active Directory B2C: REST API 클레임 교환을 오케스트레이션 단계로 통합 | Microsoft Docs'
description: API와 통합하는 Azure Active Directory B2C 사용자 지정 정책에 대한 항목입니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2017
ms.author: davidmu
ms.openlocfilehash: 0a3f69f8262e67de624d4cd97e0cad49c6a96ef0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 오케스트레이션 단계로 통합

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)의 기반이 되는 IEF(ID 경험 프레임워크)를 사용하면 ID 개발자가 사용자 경험에서 RESTful API와의 상호 작용을 통합할 수 있습니다.  

이 연습의 끝부분에서는 RESTful 서비스와 상호 작용하는 Azure AD B2C 사용자 경험을 만들 수 있습니다.

IEF는 클레임으로 데이터를 보내고 다시 클레임으로 데이터를 받습니다. REST API 클레임 교환에서는 다음을 수행할 수 있습니다.

- 오케스트레이션 단계로 설계할 수 있습니다.
- 외부 동작을 트리거할 수 있습니다. 예를 들어 외부 데이터베이스에 이벤트를 기록할 수 있습니다.
- 값을 가져와서 사용자 데이터베이스에 저장하는 데 사용할 수 있습니다.

받은 클레임은 나중에 실행 흐름을 변경하는 데 사용할 수 있습니다.

또한 상호 작용은 유효성 검사 프로필로 설계할 수 있습니다. 자세한 내용은 [연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 사용자 입력에 대한 유효성 검사로 통합](active-directory-b2c-rest-api-validation-custom.md)을 참조하세요.

시나리오는 사용자가 프로필 편집을 수행할 때 다음을 수행하려고 한다는 것입니다.

1. 외부 시스템에서 사용자를 찾습니다.
2. 해당 사용자를 등록한 도시를 가져옵니다.
3. 해당 특성을 클레임으로 응용 프로그램에 반환합니다.

## <a name="prerequisites"></a>필수 조건

- [시작](active-directory-b2c-get-started-custom.md)에서 설명한 대로 로컬 계정 등록/로그인을 완료하도록 구성된 Azure AD B2C 테넌트
- 상호 작용할 REST API 끝점 이 연습에서는 간단한 웹후크 Azure 함수 앱을 예제로 사용합니다.
- *권장*: [유효성 검증 단계로 REST API 클레임 교환을](active-directory-b2c-rest-api-validation-custom.md) 완료

## <a name="step-1-prepare-the-rest-api-function"></a>1단계 - REST API 함수 준비

> [!NOTE]
> REST API 함수 설정은 이 문서의 범위를 벗어납니다. [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference)는 클라우드에서 RESTful 서비스를 만들 수 있는 뛰어난 도구 키트를 제공합니다.

`email`이라는 클레임을 받고 할당된 `Redmond` 값이 포함된 `city` 클레임을 반환하는 Azure 함수를 설정했습니다. 샘플 Azure 함수는 [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples)에 있습니다.

Azure 함수에서 반환하는 `userMessage` 클레임은 이 컨텍스트에서 선택 사항이며 IEF에서 무시됩니다. 이 클레임은 잠재적으로 응용 프로그램에 전달되고 나중에 사용자에게 표시되는 메시지로 사용될 수 있습니다.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

Azure 함수 앱을 사용하면 특정 함수의 식별자를 포함하여 함수 URL을 쉽게 가져올 수 있습니다. 이 경우에 URL은 https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==입니다. 테스트 용도로 사용할 수 있습니다.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>2단계 - TrustFrameworkExtensions.xml 파일에서 RESTful API 클레임 교환을 기술 프로필로 구성

기술적 프로필은 RESTful 서비스에서 원하는 교환의 전체 구성입니다. TrustFrameworkExtensions.xml 파일을 열고 `<ClaimsProvider>` 요소 내에 다음 XML 코드 조각을 추가합니다.

> [!NOTE]
> 다음 XML에서 `Version=1.0.0.0` RESTful 공급자는 프로토콜로 설명되며, 외부 서비스와 상호 작용할 함수로 간주됩니다. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
            </InputClaims>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
            </OutputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`<InputClaims>` 요소는 IEF에서 REST 서비스로 전송할 클레임을 정의합니다. 이 예제에서는 REST 서비스에 `givenName` 클레임의 내용을 `email` 클레임으로 보냅니다.  

`<OutputClaims>` 요소는 IEF가 REST 서비스에서 예상하는 클레임을 정의합니다. 받은 클레임 수에 관계 없이 IEF에서는 여기서 식별된 클레임만 사용합니다. 이 예제에서는 `city`로 받은 클레임이 `city`라는 IEF 클레임에 매핑됩니다.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>3단계 - TrustFrameworkExtensions.xml 파일의 스키마에 새 `city` 클레임 추가

`city` 클레임은 아직 스키마의 어디에서도 정의되지 않습니다. 따라서 `<BuildingBlocks>` 요소 내에 정의를 추가합니다. 이 요소는 TrustFrameworkExtensions.xml 파일의 시작 부분에서 찾을 수 있습니다.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
    <ClaimsSchema>
        <ClaimType Id="city">
            <DisplayName>City</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>4단계 - TrustFrameworkExtensions.xml에서 REST 서비스 클레임 교환을 프로필 편집 사용자 경험의 오케스트레이션 단계로 포함

사용자가 인증되고(다음 XML의 1-4 오케스트레이션 단계) 업데이트된 프로필 정보를 제공하면(5단계) 프로필 편집 사용자 경험에 단계를 추가합니다.

> [!NOTE]
> REST API 호출을 오케스트레이션 단계로 사용할 수 있는 사용 사례가 많이 있습니다. 오케스트레이션 단계로서 사용자가 첫 번째 등록과 같은 태스크를 성공적으로 완료한 후 외부 시스템에 대한 업데이트로 사용하거나 동기화된 정보 상태로 유지하기 위한 프로필 업데이트로 사용할 수 있습니다. 이 경우 프로필 편집 후에 응용 프로그램에 제공된 정보를 보강하는 데 사용됩니다.

TrustFrameworkBase.xml 파일의 프로필 편집 사용자 경험 XML 코드를 `<UserJourneys>` 요소의 TrustFrameworkExtensions.xml 파일로 복사합니다. 그런 다음 6단계에서 수정합니다.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> 순서가 사용자의 버전과 일치하지 않으면 코드를 `ClaimsExchange` 형식 `SendClaims` 앞의 단계로 삽입해야 합니다.

사용자 경험의 최종 XML은 다음과 같습니다.

```XML
<UserJourney Id="ProfileEdit">
    <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
                <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
                <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
                <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>localAccountAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>socialIdpAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="5" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Add a step 6 to the user journey before the JWT token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    </OrchestrationSteps>
    <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>5단계 - `city` 클레임을 신뢰 당사자 정책 파일에 추가하여 응용 프로그램에 해당 클레임을 보냄

ProfileEdit.xml RP(신뢰 당사자) 파일을 편집하고 `<TechnicalProfile Id="PolicyProfile">` 요소를 수정하여 `<OutputClaim ClaimTypeReferenceId="city" />`를 추가합니다.

새 클레임을 추가한 후에 기술 프로필은 다음과 같습니다.

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6-upload-your-changes-and-test"></a>6단계 - 변경 내용 업로드 및 테스트

기존 버전의 정책을 덮어씁니다.

1.  (선택 사항) 계속하기 전에 기존 버전의 확장 파일을 다운로드하여 저장합니다. 초기 복잡성을 낮추려면 여러 버전의 확장 파일을 업로드하지 않는 것이 좋습니다.
2.  (선택 사항) `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`를 변경하여 정책 편집 파일에 대한 새 버전의 정책 ID의 이름을 바꿉니다.
3.  확장 파일을 업로드합니다.
4.  정책 편집 RP 파일을 업로드 합니다.
5.  **지금 실행**을 사용하여 정책을 테스트합니다. IEF에서 응용 프로그램에 반환하는 토큰을 검토합니다.

모든 항목이 올바르게 설정되면 토큰에는 `Redmond` 값의 새 클레임 `city`가 포함됩니다.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>다음 단계

[유효성 검증 단계로 REST API 사용](active-directory-b2c-rest-api-validation-custom.md)

[프로필 편집을 수정하여 사용자로부터 추가 정보 수집](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
