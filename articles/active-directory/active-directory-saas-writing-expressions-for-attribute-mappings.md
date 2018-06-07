---
title: Azure Active Directory의 특성 매핑에 대한 식 작성 | Microsoft Docs
description: Azure Active Directory에서 SaaS 앱 개체의 자동화된 프로비전 중 허용되는 형식으로 특성 값을 변환하기 위해 식 매핑을 사용하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: b13c51cd-1bea-4e5e-9791-5d951a518943
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: f1cf83044eb4f001ba341cabd0771b267c3f996d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2018
---
# <a name="writing-expressions-for-attribute-mappings-in-azure-active-directory"></a>Azure Active Directory의 특성 매핑에 대한 식 작성
SaaS 응용 프로그램에 프로비전을 구성하면 식 매핑은 지정할 수 있는 특성 매핑의 유형 중 하나입니다. 이러한 경우, 사용자의 데이터를 SaaS 응용 프로그램에 대해 사용하는 형식으로 변환할 수 있는 스크립트 방식의 식을 작성해야 합니다.

## <a name="syntax-overview"></a>구문 개요
특성 매핑을 위한 식의 구문은 VBA(Visual Basic Applications) 함수를 연상시킵니다.

* 전체 식은  <br>
  *FunctionName(<<argument 1>>,<<argument N>>)*
* 서로 함수를 중첩할 수 있습니다. 예:  <br> *FunctionOne(FunctionTwo(<<argument1>>))*
* 함수에 3가지 다른 유형의 인수를 전달할 수 있습니다.
  
  1. 특성은 대괄호로 묶어야 합니다. 예: [attributeName]
  2. 문자열 상수는 큰따옴표로 묶어야 합니다. 예: "미국"
  3. 기타 함수 예: FunctionOne(<<argument1>>, FunctionTwo(<<argument2>>))
* 문자열 상수의 경우, 백슬래시 (\) 또는 따옴표(")가 문자열에 필요한 경우 백슬래시(\) 기호로 이스케이프되어야 합니다. 예: "회사 이름: \"Contoso\""

## <a name="list-of-functions"></a>함수 목록
[Append](#append) &nbsp;&nbsp;&nbsp;&nbsp; [FormatDateTime](#formatdatetime) &nbsp;&nbsp;&nbsp;&nbsp; [Join](#join) &nbsp;&nbsp;&nbsp;&nbsp; [Mid](#mid) &nbsp;&nbsp;&nbsp;&nbsp; [Not](#not) &nbsp;&nbsp;&nbsp;&nbsp; [Replace](#replace) &nbsp;&nbsp;&nbsp;&nbsp; [SingleAppRoleAssignment](#singleapproleassignment)&nbsp;&nbsp;&nbsp;&nbsp; [StripSpaces](#stripspaces) &nbsp;&nbsp;&nbsp;&nbsp; [Switch](#switch)

- - -
### <a name="append"></a>추가
**함수:**<br> Append(source, suffix)

**설명:**<br> 원본 문자열 값을 문자열의 끝에 접미사로 추가합니다.

**매개 변수:**<br> 

| Name | 필수/ 반복 | type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |대개는 원본 개체의 특성 이름 |
| **접미사** |필수 |문자열 |원본 값의 끝에 추가하려는 문자열입니다. |

- - -
### <a name="formatdatetime"></a>FormatDateTime
**함수:**<br> FormatDateTime(source, inputFormat, outputFormat)

**설명:**<br> 한 형식의 날짜 문자열을 다른 형식으로 변환합니다.

**매개 변수:**<br> 

| Name | 필수/ 반복 | type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |대개는 원본 개체의 특성 이름입니다. |
| **inputFormat** |필수 |문자열 |원본 값의 예상된 형식입니다. 지원되는 형식은 [http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx](http://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx)를 참조하세요. |
| **outputFormat** |필수 |문자열 |출력 날짜의 형식입니다. |

- - -
### <a name="join"></a>Join
**함수:**<br> Join(separator, source1, source2, …)

**설명:**<br> 다중 **source** 문자열 값을 단일 문자열로 결합할 수 있다는 점을 제외하고 Join()은 Append()와 유사하며, 각 값은 **separator** 문자열로 구분됩니다.

원본 값 중 하나가 다중 값 특성인 경우, 해당 특성의 모든 값은 함께 조인되며 구분 기호 값을 구분합니다.

**매개 변수:**<br> 

| Name | 필수/ 반복 | type | 메모 |
| --- | --- | --- | --- |
| **구분 기호** |필수 |문자열 |문자열이 하나의 문자열로 연결되면 원본 값을 구분하는데 문자열을 사용합니다. 구분 기호가 필요하지 않은 경우 ""일 수 있습니다. |
| **source1  … sourceN ** |필수, 시간 변수 |문자열 |값이 함께 조인될 문자열입니다. |

- - -
### <a name="mid"></a>Mid
**함수:**<br> Mid(source, start, length)

**설명:**<br> 원본 값의 부분 문자열을 반환합니다. 부분 문자열은 원본 문자열에서 문자 중 일부만 포함하는 문자열입니다.

**매개 변수:**<br> 

| Name | 필수/ 반복 | type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |일반적으로 특성 이름입니다. |
| **시작** |필수 |정수 |부분 문자열이 시작될 **원본** 문자열의 인덱스입니다. 문자열의 첫번째 문자에는 인덱스 1이 있고, 두번째 문자에는 인덱스 2가 있습니다. |
| **length** |필수 |정수 |부분 문자열의 길이입니다. 길이가 **원본** 문자열 외부에서 종료되면 함수는 **시작** 인덱스부터 **원본** 문자열 끝까지의 부분 문자열을 반환합니다. |

- - -
### <a name="not"></a>not
**함수:**<br> Not(source)

**설명:**<br> **원본**의 부울 값을 대칭 이동합니다. **원본** 값이 "*True*"인 경우 "*False*"를 반환합니다. 그렇지 않은 경우 "*True*"를 반환합니다.

**매개 변수:**<br> 

| Name | 필수/ 반복 | type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |부울 문자열 |예상 **원본** 값은 "True" 또는 "False"입니다. |

- - -
### <a name="replace"></a>Replace
**함수:**<br> Replace(source, oldValue, regexPattern, regexGroupName, replacementValue, replacementAttributeName, template)

**설명:**<br>
문자열 내 값을 대체합니다. 제공된 매개 변수에 따라 다르게 작동합니다.

* **oldValue** 및 **replacementValue**가 제공되는 경우:
  
  * 소스에서 oldValue의 모든 항목을 replacementValue로 대체합니다.
* **oldValue** 및 **template**이 제공되는 경우:
  
  * **template**에서 **oldValue**의 모든 항목을 **원본** 값으로 바꿉니다.
* **regexPattern**, **regexGroupName**, **replacementValue**가 제공되는 경우:
  
  * 원본 문자열에서 OldValueRegexPattern과 일치하는 모든 값을 replacementValue로 대체합니다.
* **regexPattern**, **regexGroupName**, **replacementPropertyName**이 제공되는 경우:
  
  * **source**에 값이 없는 경우 **source**가 반환됩니다.
  * **source**에 값이 있는 경우 **regexPattern** 및 **regexGroupName**을 사용하여 **replacementPropertyName**으로 속성에서 대체 값을 추출합니다. 대체 값이 결과로 반환됩니다.

**매개 변수:**<br> 

| Name | 필수/ 반복 | type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |대개는 원본 개체의 특성 이름입니다. |
| **oldValue** |옵션 |문자열 |**원본** 또는 **템플릿**에서 대체될 값입니다. |
| **regexPattern** |옵션 |String |**원본**에서 대체될 값에 대한 Regex 패턴입니다. 또는 replacementPropertyName를 사용하면 대체 속성에서 값을 추출하는 패턴입니다. |
| **regexGroupName** |옵션 |문자열 |**regexPattern**내 그룹의 이름입니다. replacementPropertyName를 사용하는 경우에만 replacement 속성에서 replacementValue로 이 그룹의 값을 추출합니다. |
| **replacementValue** |옵션 |문자열 |이전 값과 대체할 새로운 값입니다. |
| **replacementAttributeName** |옵션 |문자열 |원본에 값이 없는 경우 대체 값에 사용할 특성의 이름입니다. |
| **template** |옵션 |문자열 |**template** 값이 제공되면, 템플릿 내에서 **oldValue**를 찾아 원본 값으로 바꿉니다. |

- - -
### <a name="singleapproleassignment"></a>SingleAppRoleAssignment
**함수:**<br> SingleAppRoleAssignment([appRoleAssignments])

**설명:**<br> 지정된 응용 프로그램에 대해 사용자에게 할당된 모든 appRoleAssignment 목록에서 단일 appRoleAssignment를 반환합니다. 이 함수는 appRoleAssignments 개체를 단일 역할 이름 문자열로 변환하는 데 필요합니다. 모범 사례는 한 번에 하나의 appRoleAssignment만 하나의 사용자에게 할당되도록 하는 것이고 여러 역할이 할당된 경우에는 반환된 역할 문자열을 예측할 수 없습니다.

**매개 변수:**<br> 

| Name | 필수/ 반복 | type | 메모 |
| --- | --- | --- | --- |
| **[appRoleAssignments]** |필수 |문자열 |**[appRoleAssignments]** 개체. |

- - -
### <a name="stripspaces"></a>StripSpaces
**함수:**<br> StripSpaces(source)

**설명:**<br> 원본 문자열에서 모든 공백(" ")을 제거합니다.

**매개 변수:**<br> 

| Name | 필수/ 반복 | type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |**원본** 값입니다. |

- - -
### <a name="switch"></a>Switch
**함수:**<br> Switch(source, defaultValue, key1, value1, key2, value2, …)

**설명:**<br> **원본** 값이 **key**와 일치하면, 해당 **key**의 **value**를 반환합니다. **원본** 값과 일치하는 키가 없으면 **defaultValue**를 반환합니다.  **Key** 및 **value** 매개 변수는 항상 쌍으로 제공되어야 합니다. 함수는 항상 짝수 개수의 매개 변수를 예상합니다.

**매개 변수:**<br> 

| Name | 필수/ 반복 | type | 메모 |
| --- | --- | --- | --- |
| **원본** |필수 |문자열 |**Source** 값입니다. |
| **defaultValue** |옵션 |문자열 |원본이 모든 키와 일치하지 않는 경우 사용할 기본값입니다. 빈 문자열("")일 수 있습니다. |
| **key** |필수 |문자열 |**원본** 값과 비교할 **Key**입니다. |
| **값** |필수 |문자열 |키와 일치하는 **원본** 의 대체 값입니다. |

## <a name="examples"></a>예
### <a name="strip-known-domain-name"></a>알려진 도메인 이름 제거
사용자 이름을 가져오려면 사용자의 전자 메일에서 알려진 도메인 이름을 제거해야 합니다. <br>
예를 들어, 도메인이 "contoso.com"인 경우 다음 식을 사용할 수 있습니다.

**식:** <br>
`Replace([mail], "@contoso.com", , ,"", ,)`

**샘플 입출력:** <br>

* **입력**(메일): “john.doe@contoso.com”
* **출력**: "john.doe"

### <a name="append-constant-suffix-to-user-name"></a>사용자 이름에 상수 접미사 추가
Salesforce 샌드박스를 사용하는 경우 동기화하기 전에 모든 사용자 이름에 추가 접미사를 추가해야할 수 있습니다.

**식:** <br>
`Append([userPrincipalName], ".test"))`

**샘플 입/출력:** <br>

* **입력**: (userPrincipalName): “John.Doe@contoso.com”
* **출력**:  “John.Doe@contoso.com.test”

### <a name="generate-user-alias-by-concatenating-parts-of-first-and-last-name"></a>이름과 성의 부분을 연결하여 사용자 별칭을 생성합니다.
사용자의 이름 중 처음 3개 문자 및 사용자 성의 처음 5개 문자를 사용하여 사용자 별칭을 생성해야 합니다.

**식:** <br>
`Append(Mid([givenName], 1, 3), Mid([surname], 1, 5))`

**샘플 입/출력:** <br>

* **입력** (givenName): "John"
* **입력** (surname): "Doe"
* **출력**: "JohDoe"

### <a name="remove-diacritics-from-a-string-and-convert-to-lowercase"></a>문자열에서 분음 부호를 제거하고 소문자로 변환
문자열에서 특수 문자를 제거하고 대문자를 소문자로 변환해야 합니다.

**식:** <br>
`Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace( Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace(Replace([givenName], , "([Øø])", , "oe", , ), , "[Ææ]", , "ae", , ), , "([äãàâãåáąÄÃÀÂÃÅÁĄA])", , "a", , ), , "([B])", , "b", , ), , "([CçčćÇČĆ])", , "c", , ), , "([ďĎD])", , "d", , ), , "([ëèéêęěËÈÉÊĘĚE])", , "e", , ), , "([F])", , "f", , ), , "([G])", , "g", , ), , "([H])", , "h", , ), , "([ïîìíÏÎÌÍI])", , "i", , ), , "([J])", , "j", , ), , "([K])", , "k", , ), , "([ľłŁĽL])", , "l", , ), , "([M])", , "m", , ), , "([ñńňÑŃŇN])", , "n", , ), , "([öòőõôóÖÒŐÕÔÓO])", , "o", , ), , "([P])", , "p", , ), , "([Q])", , "q", , ), , "([řŘR])", , "r", , ), , "([ßšśŠŚS])", , "s", , ), , "([TŤť])", , "t", , ), , "([üùûúůűÜÙÛÚŮŰU])", , "u", , ), , "([V])", , "v", , ), , "([W])", , "w", , ), , "([ýÿýŸÝY])", , "y", , ), , "([źžżŹŽŻZ])", , "z", , ), " ", , , "", , )`

**샘플 입/출력:** <br>

* **입력** (givenName): "Zoë"
* **출력**:  "zoe"

### <a name="output-date-as-a-string-in-a-certain-format"></a>특정 형식에서 문자열로 출력 날짜
SaaS 응용 프로그램에 특정 형식의 날짜를 전송하려고 합니다. <br>
예를 들어 ServiceNow에 대한 날짜 형식을 지정하려고 할 수 있습니다.

**식:** <br>

`FormatDateTime([extensionAttribute1], "yyyyMMddHHmmss.fZ", "yyyy-MM-dd")`

**샘플 입/출력:**

* **입력** (extensionAttribute1): "20150123105347.1Z"
* **출력**: “2015-01-23”

### <a name="replace-a-value-based-on-predefined-set-of-options"></a>미리 정의된 옵션 집합을 기반으로 값 바꾸기
Azure AD에 저장된 상태 코드를 기반으로 사용자의 시간대를 정의해야 합니다. <br>
상태 코드가 미리 정의된 옵션 중 하나와 일치하지 않으면 기본값인 "오스트레일리아/시드니"를 사용합니다.

**식:** <br>

`Switch([state], "Australia/Sydney", "NSW", "Australia/Sydney","QLD", "Australia/Brisbane", "SA", "Australia/Adelaide")`

**샘플 입/출력:**

* **입력** (상태): "QLD"
* **출력**: "오스트레일리아/브리즈번"

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [SaaS 앱에 자동화된 사용자 프로비전/프로비전 해제](active-directory-saas-app-provisioning.md)
* [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)
* [사용자 프로 비전에 대 한 필터 범위 지정](active-directory-saas-scoping-filters.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 응용 프로그램에 사용자 및 그룹의 자동 프로비전 사용](active-directory-scim-provisioning.md)
* [계정 프로비전 알림](active-directory-saas-account-provisioning-notifications.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)

