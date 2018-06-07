---
title: 'Azure Active Directory B2C: 사용자 지정 정책의 언어 사용자 지정 | Microsoft Docs'
description: 사용자 지정 정책에서 여러 언어로 지역화된 콘텐츠를 사용하는 방법을 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 11/13/2017
ms.author: davidmu
ms.openlocfilehash: 45cfa152615da1447cc695e0dd201e5b8d046cf4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="language-customization-in-custom-policies"></a>사용자 지정 정책의 언어 사용자 지정

> [!NOTE]
> 이 기능은 공개 미리 보기 상태입니다.
> 

사용자 지정 정책에서 언어 사용자 지정은 기본 제공 정책과 동일하게 작동합니다.  매개 변수 및 브라우저 설정에 따라 언어를 선택하는 방법에 대한 동작을 설명하는 기본 제공 [설명서](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-language-customization)를 참조하세요.

## <a name="enable-supported-languages"></a>지원되는 언어 사용
ui-locales가 지정되지 않았고 사용자의 브라우저에서 이러한 언어 중 하나를 요구하는 경우 사용자에게 지원되는 언어가 표시됩니다.  

지원되는 언어는 `<BuildingBlocks>`에서 다음 형식으로 정의됩니다.

```XML
<BuildingBlocks>
  <Localization>
    <SupportedLanguages DefaultLanguage="en">
      <SupportedLanguage>qps-ploc</SupportedLanguage>
      <SupportedLanguage>en</SupportedLanguage>
    </SupportedLanguages>
  </Localization>
</BuildingBlocks>
```

기본 언어 및 지원되는 언어는 기본 제공 정책에서 작동하는 방식과 동일하게 작동합니다.

## <a name="enable-custom-language-strings"></a>사용자 지정 언어 문자열 사용

사용자 지정 언어 문자열을 만들려면 다음 두 단계가 필요합니다.
1. 페이지에 대한 `<ContentDefinition>`을 편집하여 원하는 언어에 대한 리소스 ID를 지정합니다
2. `<BuildingBlocks>`에 해당 ID가 있는 `<LocalizedResources>`를 만듭니다.

모든 상속 정책에 변경 내용을 적용할지 여부에 따라 `<ContentDefinition>` 및 `<BuildingBlock>`을 확장 파일 또는 신뢰 정책 파일 모두에 배치할 수 있습니다.

### <a name="edit-the-contentdefinition-for-the-page"></a>페이지에 대한 ContentDefinition 편집

지역화하려는 각 페이지에 대한 각 언어 코드를 찾는 언어 리소스를 `<ContentDefinition>`에 지정할 수 있습니다.

```XML
<ContentDefinition Id="api.signuporsignin">
      <LocalizedResourcesReferences>
        <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="fr" />
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="en" />
      </LocalizedResourcesReferences>
</ContentDefinition>
```

이 샘플에서는 프랑스어(fr) 및 영어(en) 사용자 지정 문자열이 통합 등록 또는 로그인 페이지에 추가됩니다.  각 `LocalizedResourcesReference`에 대한 `LocalizedResourcesReferenceId`는 로캘과 동일하지만 모든 문자열을 ID로 사용할 수 있습니다.  각 언어 및 페이지 조합에 대해 다음과 같은 해당 `<LocalizedResources>`를 만들어야 합니다.


### <a name="create-the-localizedresources"></a>LocalizedResources 만들기

재정의는 `<BuildingBlocks>`에 포함되어 있으며, 각 페이지마다 `<ContentDefinition>`에 지정한 각 페이지 및 언어에 대한 `<LocalizedResources>`가 있습니다.  재정의 각각은 다음 샘플과 같이 `<LocalizedString>`으로 지정됩니다.

```XML
<BuildingBlocks>
  <Localization>
    <LocalizedResources Id="en">
      <LocalizedStrings>
        <LocalizedString ElementType="ClaimsProvider" StringId="SignInWithLogonNameExchange">Local Account Sign-in</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="DisplayName">Username</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="UserHelpText">Username used for signing in.</LocalizedString>
        <LocalizedString ElementType="ClaimType" ElementId="UserId" StringId="PatternHelpText">The username you provided is not valid.</LocalizedString>
        <LocalizedString ElementType="UxElement" StringId="button_signin">Sign In Now</LocalizedString>
        <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  </Localization>
</BuildingBlocks>
```

페이지에는 4가지 유형의 문자열 요소가 있습니다.

**ClaimsProvider** - ID 공급자(Facebook, Google, Azure AD 등)에 대한 레이블 **ClaimType** - 특성 및 해당 도움말 텍스트 또는 필드 유효성 검사 오류에 대한 레이블 **UxElement** - 페이지에 기본적으로 있는 다른 문자열 요소(예: 단추, 링크 또는 텍스트) **ErrorMessage** - 양식 유효성 검사 오류 메시지

이러한 재정의를 사용하는 페이지에 대해 `StringId`가 일치하는지 확인합니다. 그렇지 않으면 업로드 시 정책 유효성 검사에서 차단됩니다.  