---
title: Azure AD B2C의 언어 사용자 지정 | Microsoft Docs
description: 언어 환경을 사용자 지정하는 방법을 알아봅니다.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/26/2018
ms.author: davidmu
ms.openlocfilehash: 097033b78e3e4f640e7bf4008fd970c53315d5d7
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33200555"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 언어 사용자 지정

Azure AD B2C(Azure Active Directory B2C)의 언어 사용자 지정을 사용하면 고객 요구에 적합하도록 정책을 다른 언어로 맞출 수 있습니다.  Microsoft는 [36개 언어](#supported-languages)에 대한 번역을 제공하지만 사용자가 언어에 대한 고유한 번역을 제공할 수 있습니다. 단일 언어로만 환경이 제공되더라도 페이지에 있는 텍스트를 사용자 지정할 수 있습니다.  

## <a name="how-language-customization-works"></a>언어 사용자 지정이 작동하는 방식
언어 사용자 지정을 사용하여 사용자 경험이 제공되는 언어를 선택할 수 있습니다. 기능을 사용하도록 설정하면 사용자 응용 프로그램에서 쿼리 문자열 매개 변수 `ui_locales`를 제공할 수 있습니다. Azure AD B2C를 호출하면 페이지가 사용자가 표시한 로캘로 번역됩니다. 이 구성 형식을 통해 사용자 경험의 언어를 완전히 제어하고 고객의 브라우저 언어 설정을 무시합니다. 

고객이 볼 수 있는 언어에 대한 제어 수준이 필요하지 않을 수 있습니다. `ui_locales` 매개 변수를 제공하지 않으면 고객의 환경이 브라우저 설정으로 결정됩니다.  언어를 지원 언어로 추가하여 사용자 경험이 번역된 언어를 계속 제어할 수 있습니다. 고객의 브라우저가 지원하지 않으려는 언어를 표시하도록 설정된 경우 지원되는 문화에서 기본값으로 선택한 언어가 대신 표시됩니다.

- **ui-locales에서 지정한 언어**: 언어 사용자 지정을 사용하도록 설정한 경우 사용자 경험은 여기에 지정된 언어로 번역됩니다.
- **브라우저에서 요청한 언어**: `ui_locales` 매개 변수가 지정되지 않으면 사용자 경험이 브라우저에서 요청한 언어로 번역됩니다(*해당 언어가 지원되는 경우*).
- **정책 기본 언어**: 브라우저에서 언어를 지정하지 않거나 지원되지 않는 언어를 지정하면 사용자 경험이 정책 기본 언어로 번역됩니다.

>[!NOTE]
>사용자 지정 사용자 특성을 사용하는 경우 사용자 고유의 번역을 제공해야 합니다. 자세한 내용은 [문자열 사용자 지정](#customize-your-strings)을 참조하세요.
>

## <a name="support-requested-languages-for-uilocales"></a>ui_locales에 요청된 언어 지원 
언어 사용자 지정의 일반 공급 전에 만들어진 정책은 가장 먼저 이 기능을 사용하도록 설정해야 합니다. 그 이후에 만들어진 정책은 기본적으로 언어 사용자 지정을 사용하도록 설정됩니다. 

정책에서 언어 사용자 지정을 사용하도록 설정하면 `ui_locales` 매개 변수를 추가하여 사용자 경험의 언어를 제어할 수 있습니다.
1. [Azure Portal의 B2C 기능 페이지로 이동합니다](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings).
2. 번역에 사용할 정책을 찾아봅니다.
3. **언어 사용자 지정**을 선택합니다.  
4. **언어 사용자 지정 사용**을 선택합니다.
5. 대화 상자의 정보를 읽고 **예**를 선택합니다.

## <a name="select-which-languages-in-your-user-journey-are-enabled"></a>사용할 사용자 경험의 언어를 선택합니다. 
`ui_locales` 매개 변수 없이 브라우저에서 신청할 때 사용자 경험을 번역할 언어 집합을 설정합니다.
1. 이전 지침에서 정책에 언어 사용자 지정이 설정되었는지 확인합니다.
2. **정책 편집** 페이지에서 **언어 사용자 지정**을 선택합니다.
3. 지원하려는 언어를 선택합니다.
4. 속성 창에서 **사용**을 **예**로 전환합니다.  
5. 속성 창 위에서 **저장**을 선택합니다.

>[!NOTE]
>`ui_locales` 매개 변수가 제공되지 않으면 페이지가 고객의 브라우저 언어로 번역됩니다(사용하도록 설정된 경우에만).
>

## <a name="customize-your-strings"></a>문자열 사용자 지정
언어 사용자 지정을 통해 사용자 경험의 모든 문자열을 사용자 지정할 수 있습니다.
1. 이전 지침에서 정책에 언어 사용자 지정이 설정되었는지 확인합니다.
2. **정책 편집** 페이지에서 **언어 사용자 지정**을 선택합니다.
3. 사용자 지정하려는 언어를 선택합니다.
4. 편집하려는 페이지를 선택합니다.
5. **기본값 다운로드**(또는 이전에 이 언어를 편집한 경우 **재정의 다운로드**)를 선택합니다. 

이러한 단계는 문자열 편집을 시작하는 데 사용할 수 있는 JSON 파일을 제공합니다.

### <a name="change-any-string-on-the-page"></a>페이지에서 문자열 변경
1. 이전 지침에서 다운로드한 JSON 파일을 JSON 편집기에서 엽니다.
2. 변경할 요소를 찾습니다.  찾으려는 문자열의 `StringId`를 찾거나 변경할 `Value` 특성을 찾을 수 있습니다.
3. `Value` 특성을 표시하기를 원하는 항목으로 업데이트합니다.
4. 변경할 모든 문자열의 `Override`를 `true`로 변경합니다.
5. 파일을 저장하고 변경 내용을 업로드합니다. (JSON 파일을 다운로드한 곳과 동일한 위치에서 업로드 컨트롤을 찾을 수 있습니다.) 

>[!IMPORTANT]
>문자열을 재정의해야 하는 경우 `Override` 값을 `true`로 설정해야 합니다.  값이 변경되지 않으면 항목이 무시됩니다. 
>

### <a name="change-extension-attributes"></a>확장 특성 변경
사용자 지정 사용자 특성의 문자열을 변경하려는 경우 또는 JSON에 문자열을 추가하려는 경우 다음 형식을 사용합니다.
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

`<ExtensionAttribute>`를 사용자 지정 사용자 특성 이름으로 바꿉니다.  

`<ExtensionAttributeValue>`를 표시할 새 문자열로 바꿉니다.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>LocalizedCollections를 사용하여 값 목록을 제공합니다.
응답에 대한 값 목록 집합을 제공하려면 `LocalizedCollections` 특성을 만들어야 합니다.  `LocalizedCollections`는 `Name` 및 `Value` 쌍의 배열입니다. 항목의 순서대로 항목이 표시됩니다.  `LocalizedCollections`를 추가하려면 다음 형식을 사용하세요.

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId`는 이 `LocalizedCollections` 특성이 응답인 사용자 특성입니다.
* `Name`은 사용자에게 표시되는 값입니다.
* `Value`는 이 옵션이 선택될 때 클레임에 반환된 항목입니다.

### <a name="upload-your-changes"></a>변경 내용 업로드
1. JSON 파일 변경을 완료했으면 B2C 테넌트로 돌아갑니다.
2. **정책 편집** 페이지에서 **언어 사용자 지정**을 선택합니다.
3. 번역하려는 언어를 선택합니다.
4. 번역을 제공하려는 페이지를 선택합니다.
5. 폴더 아이콘을 선택하고 업로드할 JSON 파일을 선택합니다.
 
변경 내용이 자동으로 정책에 저장됩니다.

## <a name="customize-the-page-ui-by-using-language-customization"></a>언어 사용자 지정을 사용하여 페이지 UI 사용자 지정

HTML 콘텐츠를 지역화하는 방법은 두 가지가 있습니다. 하나는 [언어 사용자 지정](active-directory-b2c-reference-language-customization.md)을 켜는 것입니다. 이 기능을 사용하도록 설정하면 Azure AD B2C가 Open ID Connect 매개 변수 `ui-locales`를 끝점으로 전달할 수 있습니다.  콘텐츠 서버는 이 매개 변수를 사용하여 언어 관련 사용자 지정된 HTML 페이지를 제공할 수 있습니다.

또는 사용되는 로캘에 따라 다른 위치에서 콘텐츠를 끌어올 수 있습니다. CORS 사용 엔드포인트에서 특정 언어에 대한 콘텐츠를 호스트하도록 폴더 구조를 설정할 수 있습니다. 와일드 카드 값 `{Culture:RFC5646}`을 사용하는 경우 적합한 구조를 호출합니다.  예를 들어 사용자 지정 페이지 URI가 다음과 같다고 가정해 봅시다.

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
`fr`로 페이지를 로드할 수 있습니다. 페이지가 HTML 및 CSS 콘텐츠를 가져올 때 다음 위치에서 가져옵니다.
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>사용자 지정 언어 추가

또한 Microsoft가 현재 번역을 제공하지 않는 언어를 추가할 수 있습니다. 정책에서 모든 문자열에 대한 번역을 제공해야 합니다.  언어 및 로캘 코드는 ISO 639-1 표준으로 제한됩니다. 

1. **정책 편집** 페이지에서 **언어 사용자 지정**을 선택합니다.
2. 페이지의 위쪽에서 **사용자 지정 언어 추가**를 선택합니다.
3. 열린 컨텍스트 창에서 유효한 로캘 코드를 입력하여 번역을 제공하는 언어를 식별합니다.
4. 각 페이지에서 영어에 대한 재정의의 집합을 다운로드하고 번역에 사용할 수 있습니다.
5. JSON 파일을 완료하면 각 페이지에 업로드할 수 있습니다.
6. **사용**을 선택하면 이제 정책에서 사용자에게 이 언어를 표시할 수 있습니다.
7. 언어를 저장합니다.

>[!IMPORTANT]
>사용자 지정 언어를 사용하도록 설정하거나 재정의를 업로드해야 언어를 저장할 수 있습니다.
>

## <a name="additional-information"></a>추가 정보

### <a name="page-ui-customization-labels-as-overrides"></a>재정의로 페이지 UI 사용자 지정 레이블
언어 사용자 지정을 사용하도록 설정하면 페이지 UI 사용자 지정을 사용한 레이블에 대한 이전 편집이 영어(en)용 JSON 파일에서 유지됩니다. 언어 사용자 지정에서 언어 리소스를 업로드하여 레이블 및 기타 문자열을 계속해서 변경할 수 있습니다.
### <a name="up-to-date-translations"></a>최신 번역
Microsoft는 사용자가 사용할 수 있는 가장 최신의 번역을 제공하기 위해 최선을 다하고 있습니다. Microsoft는 지속적으로 번역을 개선하고 사용자에 맞게 유지할 예정입니다. Microsoft는 버그 및 글로벌 용어의 변화를 파악하고 사용자 경험에서 원활하게 작동하도록 업데이트할 예정입니다.
### <a name="support-for-right-to-left-languages"></a>오른쪽에서 왼쪽 언어 지원
현재 Microsoft는 오른쪽에서 왼쪽 언어에 대한 지원을 제공하지 않습니다. 사용자 지정 로캘을 사용하고 CSS를 사용하여 문자열이 표시되는 방식을 변경하여 지원할 수 있습니다.  이 기능이 필요한 경우 [Azure 피드백](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)에서 이 기능에 투표해 주세요.
### <a name="social-identity-provider-translations"></a>소셜 ID 공급자 변환
Microsoft는 소셜 로그인에 대한 `ui_locales` OIDC 매개 변수를 제공합니다. 하지만 Facebook과 Google을 포함한 일부 소셜 ID 공급자는 이러한 매개 변수를 인식하지 않습니다. 
### <a name="browser-behavior"></a>브라우저 동작
Chrome과 Firefox는 설정된 언어를 요청합니다. 이 언어가 지원되는 언어이면 기본 언어보다 먼저 표시됩니다. 에지에서는 현재 언어를 요청하지 않으며 기본 언어로 바로 이동합니다.

### <a name="supported-languages"></a>지원되는 언어

| 언어              | 언어 코드 |
|-----------------------|---------------|
| 벵골어                | bn            |
| 체코어                 | cs            |
| 덴마크어                | da            |
| 독일어                | de            |
| 그리스어                 | el            |
| 영어               | en            |
| 스페인어               | es            |
| 핀란드어               | fi            |
| 프랑스어                | fr            |
| 구자라트어              | gu            |
| 힌디어                 | hi            |
| 크로아티아어              | hr            |
| 헝가리어             | hu            |
| 이탈리아어               | it            |
| 일본어              | ja            |
| 카나다어               | kn            |
| 한국어                | ko            |
| 말라얄람어             | ml            |
| 마라티어               | mr            |
| 말레이어                 | ms            |
| 노르웨이어 복말      | nb            |
| 네덜란드어                 | nl            |
| 펀잡어               | pa            |
| 폴란드어                | pl            |
| 포르투갈어 - 브라질   | pt-br         |
| 포르투갈어 - 포르투갈 | pt-pt         |
| 루마니아어              | ro            |
| 러시아어               | ru            |
| 슬로바키아어                | sk            |
| 스웨덴어               | sv            |
| 타밀어                 | ta            |
| 텔루구어                | te            |
| 태국어                  | th            |
| 터키어               | tr            |
| 중국어 - 간체  | zh-hans       |
| 중국어 - 번체 | zh-hant       |
