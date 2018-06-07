---
title: Azure AD B2C에서 Application Insights의 이벤트를 사용하여 사용자 동작 추적 | Microsoft Docs
description: 사용자 지정 정책을 사용하여 Azure AD B2C 사용자 경험에서 Application Insights의 이벤트 로그를 사용하도록 설정하기 위한 단계별 가이드(미리 보기)
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 04/16/2018
ms.author: davidmu
ms.openlocfilehash: db9ba62cb6ef0d2627dbeb07cb8165a4ac7ae490
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2018
---
# <a name="track-user-behavior-in-azure-ad-b2c-journeys-by-using-application-insights"></a>Application Insights를 사용하여 Azure AD B2C 경험의 사용자 동작 추적

Azure AD B2C(Azure Active Directory B2C)는 Azure Application Insights에서 잘 작동합니다. 사용자 생성 사용자 경험에 대한 자세한 사용자 지정 이벤트 로그를 제공합니다. 이 문서에서는 시작하여 다음 작업을 수행하는 방법을 보여줍니다.

* 사용자 동작에 대한 정보를 얻습니다.
* 개발에서 또는 프로덕션 환경에서 사용자 고유의 정책 문제를 해결합니다.
* 성능을 측정합니다.
* Application Insights에서 알림을 만듭니다.

> [!NOTE]
> 이 기능은 미리 보기 상태입니다.

## <a name="how-it-works"></a>작동 방법

Azure AD B2C에서 ID 경험 프레임워크는 이제 공급자 `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`을 포함합니다.  이 공급자는 Azure AD B2C에 제공된 계측 키를 사용하여 Application Insights에 직접 이벤트 데이터를 보냅니다.

기술 프로필은 이 공급자를 사용하여 B2C에서 이벤트를 정의합니다.  이 프로필은 이벤트의 이름, 기록될 클레임 및 계측 키를 지정합니다.  이벤트를 게시하기 위해 기술 프로필이 `orchestration step` 또는 `validation technical profile`로 사용자 지정 사용자 경험에 추가됩니다.

Application Insights는 상관 관계 ID를 사용하여 사용자 세션을 기록하는 이벤트를 통합할 수 있습니다. Application Insights는 짧은 시간 안에 이벤트 및 세션을 사용할 수 있게 하며, 많은 시각화, 내보내기 및 분석 도구를 제공합니다.

## <a name="prerequisites"></a>필수 조건

[사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md)의 단계를 완료합니다. 이 문서에서는 사용자 지정 정책 시작 팩을 사용하고 있다고 가정합니다. 하지만 시작 팩은 필요하지 않습니다.

## <a name="step-1-create-an-application-insights-resource-and-get-the-instrumentation-key"></a>1단계. Application Insights 리소스 생성 및 계측 키 가져오기

Azure AD B2C에서 Application Insights를 사용하는 경우 리소스를 만들고 계측 키를 가져오기만 하면 됩니다. [Azure Portal](https://portal.azure.com)에서 리소스를 만듭니다.

1. Azure Portal의 구독 테넌트 내에서 **+ 리소스 만들기**를 선택합니다. 이 테넌트는 Azure AD B2C 테넌트가 아닙니다.  
2. **Application Insights**를 검색하고 선택합니다.  
3. 기본 설정의 구독에서 **응용 프로그램 유형**으로 **ASP.NET 웹 응용 프로그램**을 사용하는 리소스를 만듭니다.
4. Application Insights 리소스를 만든 후 열고 계측 키를 적어 둡니다.

![Application Insights 개요 및 계측 키](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## <a name="step-2-add-new-claimtype-definitions-to-your-trust-framework-extension-file"></a>2단계. 보안 프레임워크 확장 파일에 새 ClaimType 정의 추가

스타터 팩에서 확장 파일을 열고 `<BuildingBlocks>` 노드에 다음 요소를 추가합니다. 파일 이름은 일반적으로 `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`입니다.

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="step-3-add-new-technical-profiles-that-use-the-application-insights-provider"></a>3단계. Application Insights 공급자를 사용하는 새 기술 프로필 추가

기술 프로필은 Azure AD B2C의 ID 경험 프레임워크의 함수로 간주될 수 있습니다. 이 예제에서는 세션을 열고 이벤트를 게시하기 위한 5개의 기술 프로필을 정의합니다.

| 기술 프로필 | Task |
| ----------------- | -----|
| AzureInsights-Common | 모든 Azure Insights 기술 프로필에 포함되는 공통 매개 변수 집합을 만듭니다. | 
| JourneyContextForInsights | Application Insights에서 세션을 열고 상관 관계 ID를 보냅니다. |
| AzureInsights-SignInRequest | 로그인 요청이 수산될 때 클레임 집합을 사용하여 `SignIn` 이벤트를 만듭니다. | 
| AzureInsights-UserSignup | 사용자가 등록/로그인 경험에서 등록 옵션을 트리거할 때 UserSignup 이벤트를 만듭니다. | 
| AzureInsights-SignInComplete | 토큰이 신뢰 당사자 응용 프로그램에 전송되었을 때 인증이 성공적으로 완료되었다고 기록합니다. | 

`<ClaimsProviders>` 노드에 다음 요소를 추가하여 스타터 팩에서 확장 파일로 프로필을 추가합니다.  파일 이름은 일반적으로 `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`입니다.

> [!IMPORTANT]
> `ApplicationInsights-Common` 기술 프로필의 계측 키를 Application Insights 리소스에서 제공하는 GUID로 변경합니다.

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="JourneyContextForInsights">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
      </OutputClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-4-add-the-technical-profiles-for-application-insights-as-orchestration-steps-in-an-existing-user-journey"></a>4단계. Application Insights에 대한 기술 프로필을 기존 사용자 경험에 오케스트레이션 단계로 추가

`JournyeContextForInsights`를 오케스트레이션 1단계로 호출합니다.

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
  </ClaimsExchanges>
</OrchestrationStep>
```

`Azure-Insights-SignInRequest`를 오케스트레이션 2단계로 호출하여 로그인/등록 요청이 수신되었는지 추적합니다.

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

`SendClaims` 오케스트레이션 단계 바로 *전에* `Azure-Insights-UserSignup`을 호출하는 새 단계를 추가합니다. 사용자가 등록/로그인 경험에서 등록 단추를 선택할 때 트리거됩니다.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="9" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
```

`SendClaims` 오케스트레이션 단계 바로 다음에 `Azure-Insights-SignInComplete`를 호출합니다. 이 단계는 성공적으로 완료된 경험을 반영합니다.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="11" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> 새 오케스트레이션 단계를 추가한 후, 1부터 N까지 건너뛰지 말고 순차적으로 단계 번호를 다시 매깁니다.


## <a name="step-5-upload-your-modified-extensions-file-run-the-policy-and-view-events-in-application-insights"></a>5단계. Application Insights에서 수정된 확장 파일 업로드, 정책 실행 및 이벤트 보기

새 보안 프레임워크 확장 파일을 저장하고 업로드합니다. 그런 후 응용 프로그램에서 신뢰 당사자 정책을 호출하거나 Azure AD B2C 인터페이스에서 `Run Now`를 사용합니다. 몇 초 내에 사용자 이벤트를 Application Insights에서 사용할 수 있습니다.

1. Azure Active Directory 테넌트에서 **Application Insights** 리소스를 엽니다.
2. **사용량** > **이벤트**를 선택합니다.
3. **During**을 **지난 1시간**으로 설정하고 **By**를 **3분**으로 설정합니다.  결과를 보기 위해 **새로 고침**을 선택해야 할 수도 있습니다.

![Application Insights USAGE-Events Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

##  <a name="next-steps"></a>다음 단계

필요에 맞게 사용자 경험에 클레임 유형 및 이벤트를 추가합니다. 다음은 추가 클레임 해결 프로그램을 사용하는 가능한 클레임 목록입니다.

### <a name="culture-specific-claims"></a>문화권 관련 클레임

```xml
Referenced using: {Culture:One of the property names below}
```

| 클레임 | 정의 | 예 |
| ----- | -----------| --------|
| LanguageName | 언어에 대한 2자 ISO 코드  | en |
| RegionName | 지역에 대한 2자 ISO 코드  | US |
| RFC5646 | RFC5646 언어 코드 | ko-KR |
| LCID   | 언어 코드의 LCID | 1033 |

### <a name="policy-specific-claims"></a>정책 관련 클레임

```xml
Referenced using {Policy:One of the property names below}
```

| 클레임 | 정의 | 예 |
| ----- | -----------| --------|
| TrustFrameworkTenantId | trustframework 테넌트 ID | 해당 없음 |
| RelyingPartyTenantId | 신뢰 당사자의 테넌트 ID | 해당 없음 |
| PolicyId | 정책의 개체 ID | 해당 없음 |
| TenantObjectId | 정책의 테넌트 개체 ID | 해당 없음 |

### <a name="openid-connect-specific-claims"></a>OpenID Connect 관련 클레임

```xml
Referenced using {OIDC:One of the property names below}
```

| 클레임 | OpenIdConnect 매개 변수 | 예 |
| ----- | ----------------------- | --------|
| prompt | prompt | 해당 없음 |
| LoginHint |  login_hint | 해당 없음 |
| DomainHint | domain_hint | 해당 없음 |
|  MaxAge | max_age | 해당 없음 |
| clientid | client_id | 해당 없음 |
| 사용자 이름 | login_hint | 해당 없음 |
| 암호 | domain_hint | 해당 없음 |
|  리소스 | resource| 해당 없음 |
| AuthenticationContextReferences | acr_values | 해당 없음 |

### <a name="non-protocol-parameters-included-with-oidc--oauth2-requests"></a>OIDC 및 OAuth2 요청에 포함된 비 프로토콜 매개 변수

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

OIDC 또는 OAuth2 요청의 일부로 포함된 모든 매개 변수 이름은 사용자 경험에서 클레임에 매핑될 수 있습니다. 그런 다음, 이벤트에 기록할 수 있습니다. 예를 들어, 응용 프로그램의 요청에는 이름이 `app_session`, `loyalty_number` 또는 `any_string`인 쿼리 문자열 매개 변수가 포함될 수 있습니다.

다음은 응용 프로그램의 샘플 요청입니다.

```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
Application Insights 이벤트에 `Input Claim` 요소를 추가하여 클레임을 추가할 수 있습니다.

```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### <a name="other-system-claims"></a>기타 시스템 클레임

일부 시스템 클레임은 레코드에서 이벤트로 사용되려면 클레임 모음에 추가되어야 합니다. 이러한 클레임을 사용하려면 기술 프로필 `SimpleUJContext`를 오케스트레이션 단계 또는 유효성 검사 기술 프로필로 호출해야 합니다.

```xml
<ClaimsProvider>
  <DisplayName>User Journey Context Provider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SimpleUJContext">
      <DisplayName>User Journey Context Provide</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="IP-Address" />
        <OutputClaim ClaimTypeReferenceId="CorrelationId" />
        <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
        <OutputClaim ClaimTypeReferenceId="Build" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```


