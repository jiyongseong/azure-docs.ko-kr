---
title: 사용자 지정 정책의 암호 복잡성 - Azure AD B2C | Microsoft Docs
description: 사용자 지정 정책에서 암호에 복잡성 요구 사항을 구성하는 방법
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: 9f8d576cbc5c2bb2fe4109086b04711422911390
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="configure-password-complexity-in-custom-policies"></a>사용자 지정 정책에서 암호 복잡성 구성

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서는 암호 복잡성이 작동하는 방법의 고급 설명이며 Azure AD B2C 사용자 지정 정책을 사용하여 활성화됩니다.

## <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: 암호에 복잡성 요구 사항 구성

Azure AD B2C(Azure Active Directory B2C)는 계정을 만들 때 최종 사용자가 제공하는 암호에 복잡성 요구 사항을 변경하도록 지원합니다.  기본적으로 Azure AD B2C는 **강력한** 암호를 사용합니다.  또한 Azure AD B2C는 고객이 사용할 수는 암호의 복잡성을 제어하는 구성 옵션을 지원합니다.  이 문서는 사용자 지정 정책에서 암호 복잡성을 구성하는 방법에 대해 설명합니다.  [기본 제공 정책에서 암호 복잡성 구성](active-directory-b2c-reference-password-complexity.md)을 사용할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

[시작](active-directory-b2c-get-started-custom.md)에서 설명한 대로 로컬 계정 등록/로그인을 완료하도록 구성된 Azure AD B2C 테넌트

## <a name="how-to-configure-password-complexity-in-custom-policy"></a>사용자 지정 정책에서 암호 복잡성을 구성하는 방법

사용자 지정 정책에서 암호 복잡성을 구성하려면 사용자 지정 정책의 전체 구조가 `BuildingBlocks` 내에서 `ClaimsSchema`, `Predicates` 및 `InputValidations` 요소를 포함해야 합니다.

```XML
  <BuildingBlocks>
    <ClaimsSchema>...</ClaimsSchema>
    <Predicates>...</Predicates>
    <InputValidations>...</InputValidations>
  </BuildingBlocks>
```

이러한 요소의 용도는 다음과 같습니다.

- 각 `Predicate` 요소는 true 또는 false를 반환하는 기본 문자열 유효성 검사를 정의합니다.
- `InputValidations` 요소에는 하나 이상의 `InputValidation` 요소가 있습니다.  각 `InputValidation`은 일련의 `Predicate` 요소를 사용하여 생성됩니다. 이 요소를 사용하면 부울 집계를 수행할 수 있습니다(`and` 및 `or` 유사).
- `ClaimsSchema`는 유효성 검사 중인 클레임을 정의합니다.  그런 다음 해당 클레임의 유효성을 검사하는 데 사용되는 `InputValidation` 규칙을 정의합니다.

### <a name="defining-a-predicate-element"></a>조건자 요소 정의

조건자에는 IsLengthRange 또는 MatchesRegex와 같은 두 개의 메서드 형식이 있습니다. 각각의 예제를 검토해 보겠습니다.  먼저 정규식을 일치시키는 데 사용되는 MatchesRegex 예제가 있습니다.  이 예제에서는 숫자가 포함된 문자열을 일치시킵니다.

```XML
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
```

다음으로 IsLengthRange 예제를 살펴 보겠습니다.  이 메서드는 최소 및 최대 문자열 길이를 사용합니다.

```XML
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
```

`HelpText` 특성을 사용하여 확인이 실패하는 경우 최종 사용자에게 오류 메시지를 제공합니다.  이 문자열은 [언어 사용자 지정 기능](active-directory-b2c-reference-language-customization.md)을 사용하여 지역화될 수 있습니다.

### <a name="defining-an-inputvalidation-element"></a>InputValidation 요소 정의

`InputValidation`은 `PredicateReferences`라는 집계를 수행합니다. `InputValidation`이 성공하기 위해 각 `PredicateReferences`는 true여야 합니다.  그러나 `PredicateReferences` 요소는 `MatchAtLeast`라는 특성을 사용하여 true를 반환해야 하는 `PredicateReference` 검사의 개수를 지정합니다.  필요에 따라 `HelpText` 특성을 정의하여 참조하는 `Predicate` 요소에 정의된 오류 메시지를 재정의합니다.

```XML
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
```

### <a name="defining-a-claimsschema-element"></a>ClaimsSchema 요소 정의

`newPassword` 및 `reenterPassword` 클레임 형식은 특수한 것으로 간주되므로 이름을 변경하지 않습니다.  UI는 이러한 `ClaimType` 요소에 기반하여 계정을 만드는 동안 사용자가 해당 암호를 올바르게 다시 입력했는지 유효성을 검사합니다.  동일한 `ClaimType` 요소를 찾으려면 시작 팩에 있는 TrustFrameworkBase.xml에서 찾습니다.  `InputValidationReference`를 정의하는 이러한 요소를 재정의하는 것이 이 예제의 새로운 기능입니다. 새 요소의 `ID` 특성은 정의한 `InputValidation` 요소를 가리킵니다.

```XML
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
```

### <a name="putting-it-all-together"></a>모든 항목 요약

이 예에서는 모든 작업을 수행하여 작업 정책을 형성하는 방법을 표시합니다.  이 예제를 사용하려면:

1. [시작](active-directory-b2c-get-started-custom.md) 필수 구성 요소의 지침에 따라 TrustFrameworkBase.xml 및 TrustFrameworkExtensions.xml을 다운로드하고, 구성하고 업로드합니다.
1. 이 섹션에서 예제 콘텐츠를 사용하여 SignUporSignIn.xml 파일을 만듭니다.
1. `yourtenant`를 Azure AD B2C 테넌트 이름으로 바꿔서 SignUporSignIn.xml을 업데이트합니다.
1. 마지막으로 SignUporSignIn.xml 정책 파일을 업로드합니다.

이 예제에는 PIN 암호에 대한 유효성 검사 및 강력한 암호에 대한 유효성 검사가 포함되어 있습니다.

- `PINpassword`를 찾아보세요. 이 `InputValidation` 요소는 모든 길이의 PIN 유효성을 검사합니다.  `ClaimType` 내의 `InputValidationReference` 요소에서 참조되지 않기 때문에 현재 사용되지 않습니다. 
- `PasswordValidation`를 찾아보세요. 이 `InputValidation` 요소는 암호가 8~16자이며 대문자, 소문자, 숫자, 기호라는 4개 항목 중 3가지를 포함하는지 확인합니다.  `ClaimType`에서 참조됩니다.  따라서 이 규칙은 이 정책에 적용되고 있습니다.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
 <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="newPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
      <ClaimType Id="reenterPassword">
        <InputValidationReference Id="PasswordValidation" />
      </ClaimType>
    </ClaimsSchema>
    <Predicates>
      <Predicate Id="Lowercase" Method="MatchesRegex" HelpText="a lowercase">
        <Parameters>
          <Parameter Id="RegularExpression">[a-z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="MatchesRegex" HelpText="an uppercase">
        <Parameters>
          <Parameter Id="RegularExpression">[A-Z]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="MatchesRegex" HelpText="a number">
        <Parameters>
          <Parameter Id="RegularExpression">[0-9]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="MatchesRegex" HelpText="a symbol">
        <Parameters>
          <Parameter Id="RegularExpression">[!@#$%^*()]+</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Length" Method="IsLengthRange" HelpText="The password must be between 8 and 16 characters.">
        <Parameters>
          <Parameter Id="Minimum">8</Parameter>
          <Parameter Id="Maximum">16</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be a pin.">
        <Parameters>
          <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    <InputValidations>
      <InputValidation Id="PasswordValidation">
        <PredicateReferences Id="LengthGroup" MatchAtLeast="1">
          <PredicateReference Id="Length" />
        </PredicateReferences>
        <PredicateReferences Id="3of4" MatchAtLeast="3" HelpText="You must have at least 3 of the following character classes:">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </InputValidation>
      <InputValidation Id="PINpassword">
        <PredicateReferences Id="PINGroup">
          <PredicateReference Id="PIN" />
        </PredicateReferences>
      </InputValidation>
    </InputValidations>
  </BuildingBlocks>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword" />
      </InputClaims>
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
</TrustFrameworkPolicy>
```
