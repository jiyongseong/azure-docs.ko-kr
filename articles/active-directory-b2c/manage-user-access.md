---
title: Azure AD B2C에서 사용자 액세스 관리 | Microsoft Docs
description: Azure AD B2C를 사용하여 미성년자를 식별하고, 생년월일 및 국가 데이터를 수집하고, 응용 프로그램의 사용 약관에 수락을 받는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/04/2018
ms.author: davidmu
ms.openlocfilehash: c44135a3069966b14d8760e4daa009ab8d39ccca
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932541"
---
# <a name="manage-user-access-in-azure-ad-b2c"></a>Azure AD B2C에서 사용자 액세스 관리

이 문서에서는 Azure AD(Active Directory) B2C를 사용하여 응용 프로그램에 대한 사용자 액세스를 관리하는 방법에 대한 정보를 제공합니다. 응용 프로그램의 액세스 관리에는 다음 항목이 포함됩니다.

- 미성년자 식별 및 응용 프로그램 사용을 위한 액세스 제어
- 응용 프로그램을 사용하는 미성년자에 대한 보호자의 동의 요구
- 사용자의 생일 및 국가 데이터 수집
- 사용 약관 규약 및 액세스 제한 포착

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

>[!Note] 
>이 문서는 GDPR 하에서 의무를 지원하는 데 사용할 수 있는 정보를 제공합니다. GDPR에 대한 일반적인 내용은 [Service Trust Portal의 GDPR 섹션](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted)을 참조하세요.

## <a name="control-minor-access"></a>미성년자 액세스 제어

응용 프로그램 및 조직은 미성년자를 대상으로 하지 않는 응용 프로그램 및 서비스를 미성년자가 사용하지 못하도록 차단할 수 있습니다. 또는 응용 프로그램 및 조직에서 미성년자를 받아들인 후 보호자의 동의를 관리하고 비즈니스 규칙에 규정되고 규정에 따라 허용되는 경험을 미성년자에게 제공할 수 있습니다. 

사용자가 미성년자로 식별되면 Azure AD B2C의 사용자 흐름은 세 가지 옵션 중 하나로 설정될 수 있습니다.

- **응용 프로그램에 서명된 JWT id_token 보내기** - 사용자가 디렉터리에 등록되고 토큰이 응용 프로그램으로 반환됩니다. 그런 다음, 응용 프로그램이 비즈니스 규칙을 사용하여 진행됩니다. 예를 들어, 응용 프로그램이 보호자의 동의 프로세스를 진행할 수 있습니다. 이를 위해 응용 프로그램으로부터 **ageGroup** 및 **consentProvidedForMinor** 클레임을 수신하도록 선택합니다.
- **응용 프로그램에 서명되지 않은 JSON 토큰 보내기** - Azure AD B2C가 응용 프로그램에 사용자가 미성년자임을 알리고 사용자의 보호자 동의 상태를 제공합니다. 그런 다음, 응용 프로그램이 비즈니스 규칙을 사용하여 진행됩니다. JSON 토큰이 응용 프로그램 인증을 성공적으로 완료하지 못합니다. 응용 프로그램은 JSON 토큰에 포함된 클레임에 따라 인증되지 않은 사용자를 처리해야 합니다. 여기에는 **name**, **email**, **ageGroup** 및 **consentProvidedForMinor**가 포함될 수 있습니다.
- **사용자 차단** - 사용자가 미성년자이고 보호자의 동의가 제공되지 않은 경우,  Azure AD B2C는 차단된 사실을 알리는 화면을 사용자에게 표시할 수 있습니다.  토큰이 발급되지 않고 액세스가 차단되고 등록 과정에서 사용자 계정이 만들어지지 않습니다. 이렇게 구현하려면 사용자에게 알리고 적절한 옵션을 제시하기에 적합한 HTML/CSS 콘텐츠 페이지를 제공합니다. 응용 프로그램에서 신규 등록을 위한 추가 작업이 필요하지 않습니다.

## <a name="get-parental-consent"></a>보호자 동의 얻기

응용 프로그램 규정에 따라 성인으로 확인된 보호자의 동의가 필요할 수 있습니다.  Azure AD B2C는 개인의 나이를 확인하여 확인된 성인이 미성년자에게 보호자 동의를 부여하는 환경을 제공하지 않습니다.  이러한 환경은 응용 프로그램이나 다른 서비스 공급자가 제공해야 합니다.

다음은 보호자 동의를 수집하는 사용자 흐름의 예입니다.

1. [Azure Active Directory Graph API](https://msdn.microsoft.com/en-us/library/azure/ad/graph/api/api-catalog) 작업이 사용자를 미성년자로 식별하고 서명되지 않은 JSON 토큰의 형태로 사용자 데이터를 응용 프로그램에 반환합니다.
2. 응용 프로그램이 JSON 토큰을 처리하고 보호자 동의가 필요하다고 알리는 화면을 미성년자에게 표시하고 온라인으로 보호자의 동의를 요청합니다. 
3. Azure AD B2C는 사용자가 정상적으로 로그인하고 **legalAgeGroupClassification = “minorWithParentalConsent”** 를 포함하도록 설정된 토큰을 응용 프로그램에 발행할 수 있는 로그인 과정을 표시합니다. 응용 프로그램은 보호자의 이메일 주소를 수집하고 주민등록증, 면허증 검증 또는 신용 카드 증명과 같이 신뢰할 수 있는 소스를 사용하여 보호자가 성인인지 확인합니다. 성공한 경우, 응용 프로그램은 미성년자에게 Azure AD B2C 사용자 흐름을 사용하여 로그인하라는 메시지를 표시합니다. 동의가 거부되면(예: **legalAgeGroupClassification = “minorWithoutParentalConsent”**) Azure AD B2C는 동의 프로세스를 다시 시작하도록 응용 프로그램에 JSON 토큰(로그인 아님)을 반환합니다. 미성년자의 이메일 주소 또는 기록에 있는 성인의 이메일 주소로 등록 코드를 보내서 미성년자 또는 성인이 미성년자 계정에 대한 액세스를 다시 얻을 수 있도록 사용자 흐름을 사용자 지정하는 것도 선택적으로 가능합니다.
4. 응용 프로그램이 미성년자에게 동의 철회 옵션을 제공합니다.
5. 미성년자 또는 성인이 동의를 철회하면 Azure AD Graph API를 사용하여 **consetProvidedForMinor**를 **denied**로 변경할 수 있습니다. 또는 응용 프로그램이 동의가 철회된 미성년자를 삭제하도록 선택할 수도 있습니다. 인증된 미성년자(또는 미성년자의 계정을 사용하는 보호자)가 동의를 철회할 수 있도록 사용자 흐름을 사용자 지정하는 것도 선택적으로 가능합니다. Azure AD B2C가 **consentProvidedForMinor**를 **denied**로 기록합니다.

**legalAgeGroupClassification**, **consentProvidedForMinor** 및 **ageGroup**에 대한 자세한 내용은 [사용자 리소스 유형](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/user)을 참조하세요. 사용자 지정 특성에 대한 자세한 내용은 [사용자 지정 특성을 사용하여 소비자에 대한 정보를 수집합니다.](active-directory-b2c-reference-custom-attr.md)를 참조하세요. Azure AD Graph API를 사용하여 확장된 특성을 처리하는 경우 특성의 긴 버전(예: "extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth": "2011-01-01T00:00:00Z")을 사용해야 합니다.

## <a name="gather-date-of-birth-and-country-data"></a>생년월일 및 국가 데이터 수집

등록하는 동안 응용 프로그램은 Azure AD B2C를 사용하여 모든 사용자의 DOB(생년월일)와 국가를 수집할 수 있습니다. DOB 또는 국가 정보가 아직 없는 경우 다음 인증(로그인) 과정에서 사용자에게 요청할 수 있습니다. 사용자는 DOB 및 국가 정보를 제공하지 않고는 진행할 수 없습니다. 제공된 국가 및 DOB를 기반으로, Azure AD B2C는 해당 국가의 규제 기준에 따라 해당 개인이 미성년자로 간주되는지 여부를 판단합니다. 

사용자 지정된 사용자 흐름은 DOB 및 국가 정보를 수집하고 Azure AD B2C 클레임 변환을 사용하여 **ageGroup**을 판별하고 결과를(또는 국가 및 DOB 정보를 직접) 디렉터리에 보존할 수 있습니다.

아래 단계는 생년월일에서 **ageGroup**을 계산하는 데 사용되는 논리를 보여줍니다.

1. 목록에 있는 국가 코드로 국가를 찾습니다. 해당 국가를 찾을 수 없는 경우 **기본값**으로 되돌립니다.
2. **MinorConsent** 노드가 country 요소에 있는 경우:  <br>a. 성인으로 간주되기 위해 사용자가 태어났어야 하는 최소 날짜를 계산합니다. 예: 생년월일이 3/14/2015이고 **MinorConsent**는 18이며 최소 생년월일은 3/14/2000이 됩니다.
    <br>나. 최소 생년월일과 실제 생년월일을 비교합니다. 최소 생년월일이 사용자 생년월일 이전이면, 계산은 연령 그룹 계산으로 **Minor**를 반환합니다.
3. **MinorNoConsentRequired** 노드가 country 요소에 있으면, **MinorNoConsentRequired**의 값을 사용하여 2a 및 2b 단계를 반복합니다. 최소 생년월일이 사용자 생년월일 이전이면 2b의 출력은 **MinorNoConsentRequired**를 반환합니다. 
4. 두 계산 중 어느 것도 true를 반환하지 않으면 계산은 **Adult**를 반환합니다.

응용 프로그램이 DOB 또는 국가 데이터를 다른 방법으로 안정적으로 수집한 경우, 응용 프로그램은 GRAPH API를 사용하여 이 정보로 사용자 레코드를 업데이트할 수 있습니다. 예: 

- 사용자가 성인으로 알려지면 디렉터리 특성인 **ageGroup**의 값을 **Adult**로 업데이트합니다.
- 사용자가 미성년자로 알려지면 디렉터리 특성인 **ageGroup**의 값을 **Minor**로 업데이트하고 **consentProvidedForMinor**를 적절하게 설정합니다.

DOB 데이터 수집에 대한 자세한 내용은 [Azure AD B2C에서 나이 게이팅 사용](basic-age-gating.md)을 참조하세요.

## <a name="capture-terms-of-use-agreement"></a>사용 약관 규약 포착

응용 프로그램을 개발하는 경우 일반적으로 사용자 디렉터리에서 참여하지 않거나 사소한 참여만으로 응용 프로그램 내에서 사용 약관에 대한 사용자 수락을 포착합니다.  하지만 Azure AD B2C 사용자 흐름을 사용하여 사용 약관에 대한 규약을 수집하고 수락이 허락되지 않으면 액세스를 제한할 수 있으며 마지막 수락 날짜 및 사용 약관의 최신 버전 날짜를 기반으로 사용 약관에 대한 향후 변경 사항을 수락하도록 할 수 있습니다.

**사용 약관**에는 “타사와 데이터 공유에 대한 동의”를 포함할 수도 있습니다.  이러한 조건에 대한 사용자의 긍정적인 수락은 기술적으로 결합된 상태로 수집할 수 있습니다. 또는 사용자가 현지 규정 및 비즈니스 규칙에 따라 하나는 수락하고 다른 항목은 수락하지 않을 수 있습니다.

아래 단계는 사용 약관을 관리하는 기능을 설명합니다.

1. Graph API 및 확장된 특성 사용에 관한 사용 약관에 대한 수락 및 수락 날짜를 기록합니다. 이 과정은 기본 제공 사용자 흐름 및 사용자 지정 사용자 흐름을 사용하여 수행할 수 있습니다. **extension_termsOfUseConsentDateTime** 및 **extension_termsOfUseConsentVersion** 특성을 만들고 사용하는 것이 좋습니다.
2. “사용 약관에 동의”라는 필수 확인란을 만들고 등록하는 동안 결과를 기록합니다. 이 과정은 기본 제공 사용자 흐름 및 사용자 지정 사용자 흐름을 사용하여 수행할 수 있습니다.
3. Azure AD B2C가 사용 약관 규약 및 동의를 저장합니다. Graph API는 응답을 기록하는 데 사용되는 확장 특성을 읽어서 사용자의 상태를 쿼리하는 데 사용됩니다. 예를 들어 **termsOfUseTestUpdateDateTime**을 읽습니다. 이 과정은 기본 제공 사용자 흐름 및 사용자 지정 사용자 흐름을 사용하여 수행할 수 있습니다.
4. 수락 날짜와 사용 약관 최신 버전의 날짜를 비교하여 업데이트된 사용 약관에 대한 수락을 요구합니다. 이 작업은 사용자 지정 사용자 흐름을 통해서만 수행할 수 있습니다. 확장된 특성인 **extension_termsOfUseConsentDateTime**을 사용하여 **termsOfUseTextUpdateDateTime**의 클레임에 값을 비교하고 수락 날짜가 더 오래되었으면 자체 어설션된 새로운 수락 화면을 표시하고 수락하지 않으면 정책 논리를 사용하여 액세스를 차단합니다.
5. 수락한 버전 번호를 마지막으로 수락한 버전 번호와 비교하여 업데이트된 사용 약관에 대한 수락을 요구합니다. 이 작업은 사용자 지정 사용자 흐름을 통해서만 수행할 수 있습니다. 확장된 특성인 **extension_termsOfUseConsentDateTime**을 사용하여 **extension_termsOfUseConsentVersion**의 클레임에 값을 비교하고 수락 날짜가 더 오래되었으면 자체 어설션된 새로운 수락 화면을 표시하고 수락하지 않으면 정책 논리를 사용하여 액세스를 차단합니다.

사용 약관에 대한 동의 포착은 아래와 같은 시나리오에서 표시할 수 있습니다.

- 새로운 사용자가 등록 중입니다. 사용 약관이 표시되고 동의 결과가 저장됩니다.
- 사용자가 로그인 중이며 최신 또는 활성 사용 약관에 대한 동의를 이미 수락한 적이 있습니다. 사용 약관이 표시되지 않습니다.
- 사용자가 로그인 중이고 최신 또는 활성 사용 약관에 대한 동의를 아직 수락하지 않았습니다. 사용 약관이 표시되고 동의 결과가 저장됩니다.
- 사용자가 로그인 중이며 이전 사용 약관에 대한 동의를 이미 수락했지만 약관이 최신 버전으로 업데이트되었습니다. 사용 약관이 표시되고 동의 결과가 저장됩니다.

다음 이미지에서는 권장되는 사용자 흐름을 보여줍니다.

![수락 사용자 흐름](./media/manage-user-access/user-flow.png) 

아래는 클레임의 DateTime 기반 사용 약관 동의의 예입니다.

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

아래는 클레임의 Version 기반 사용 약관 동의의 예입니다.

```
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations> 
```

## <a name="next-steps"></a>다음 단계

- [사용자 데이터 관리](manage-user-data.md)에서 사용자 데이터를 삭제하고 내보내는 방법 알아보기
