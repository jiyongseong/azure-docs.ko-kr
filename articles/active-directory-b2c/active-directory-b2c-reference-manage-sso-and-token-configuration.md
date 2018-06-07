---
title: 사용자 지정 정책을 사용하여 SSO 및 토큰 사용자 지정 관리 | Microsoft Docs
description: 사용자 지정 정책을 사용하여 SSO 및 토큰 사용자 지정을 관리하는 방법에 대해 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/02/2017
ms.author: davidmu
ms.openlocfilehash: c9eb7f7711a8987945b8aeaee8d6798b7a6b8284
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C: 사용자 지정 정책을 사용하여 SSO 및 토큰 사용자 지정 관리
사용자 지정 정책을 사용하면 토큰, 세션 및 SSO(Single Sign-On) 구성에 대해 기본 정책을 통할 때와 동일한 제어가 제공됩니다.  각 설정에 대해 알아보려면 [여기](#active-directory-b2c-token-session-sso) 설명서를 참조하세요.

## <a name="token-lifetimes-and-claims-configuration"></a>토큰 수명 및 클레임 구성
토큰 수명에 대한 설정을 변경하려면 영향을 줄 정책의 신뢰 당사자 파일에 `<ClaimsProviders>` 요소를 추가해야 합니다.  `<ClaimsProviders>` 요소는 `<TrustFrameworkPolicy>`의 자식 요소입니다.  이 안에 토큰 수명에 영향을 주는 정보를 입력해야 합니다.  XML은 다음과 같이 표시됩니다.

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

**액세스 토큰 수명** `<Item>` 안의 값을 Key="token_lifetime_secs"(초)로 수정하여 액세스 토큰 수명을 변경할 수 있습니다.  기본 제공되는 기본값은 3600초(60분)입니다.

**ID 토큰 수명** `<Item>` 안의 값을 Key="id_token_lifetime_secs"(초)로 수정하여 ID 토큰 수명을 변경할 수 있습니다.  기본 제공되는 기본값은 3600초(60분)입니다.

**새로 고침 토큰 수명** `<Item>` 안의 값을 Key="refresh_token_lifetime_secs"(초)로 수정하여 새로 고침 토큰 수명을 변경할 수 있습니다.  기본 제공되는 기본값은 1209600초(14일)입니다.

**새로 고침 토큰 슬라이딩 윈도우 수명** 새로 고침 토큰에 대한 슬라이딩 윈도우 수명을 설정하려면 `<Item>` 안의 값을 Key="rolling_refresh_token_lifetime_secs"(초)로 수정합니다.  기본 제공되는 기본값은 7776000초(90일)입니다.  슬라이딩 윈도우 수명을 적용하지 않으려면 이 줄을 다음으로 바꿉니다.
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**발급자(iss) 클레임** 발급자(iss) 클레임을 변경하려면 `<Item>` 안의 값을 Key="IssuanceClaimPattern"으로 수정합니다.  적용 가능한 값은 `AuthorityAndTenantGuid` 및 `AuthorityWithTfp`입니다.

**정책 ID를 나타내는 클레임 설정** 이 값을 설정하기 위한 옵션은 TFP(보안 프레임워크 정책) 및 ACR(인증 컨텍스트 참조)입니다.  
TFP로 설정하는 것이 좋으며 이렇게 하려면 Key="AuthenticationContextReferenceClaimPattern"인 `<Item>`이 존재하고 값이 `None`인지 확인합니다.
`<OutputClaims>` 항목에서 이 요소를 추가합니다.
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
ACR의 경우 Key="AuthenticationContextReferenceClaimPattern"인 `<Item>`을 제거합니다.

**주체(sub) 클레임** 이 옵션은 기본적으로 ObjectID입니다. 이 값을 `Not Supported`로 전환하려면 다음을 수행합니다.

다음 줄을 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
다음 줄로 바꿉니다.
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>세션 동작 및 SSO
세션 동작 및 SSO 구성을 변경하려면 `<RelyingParty>` 요소 내에 `<UserJourneyBehaviors>` 요소를 추가해야 합니다.  `<UserJourneyBehaviors>` 요소 바로 뒤에는 `<DefaultUserJourney>`가 나와야 합니다.  `<UserJourneyBehavors>` 요소 내부는 다음과 같아야 합니다.

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**SSO(Single Sign-On) 구성** SSO(Single Sign-On) 구성을 변경하려면 `<SingleSignOn>` 값을 수정해야 합니다.  적용 가능한 값은 `Tenant`, `Application`, `Policy` 및 `Disabled`입니다. 

**웹앱 세션 수명(분)** 웹앱 세션 수명을 변경하려면 `<SessionExpiryInSeconds>` 요소의 값을 수정해야 합니다.  기본 제공 정책의 기본값은 86400초(1440분)입니다.

**웹앱 세션 시간 제한** 웹앱 세션 시간 제한을 변경하려면 `<SessionExpiryType>` 값을 수정해야 합니다.  적용 가능한 값은 `Absolute` 및 `Rolling`입니다.
