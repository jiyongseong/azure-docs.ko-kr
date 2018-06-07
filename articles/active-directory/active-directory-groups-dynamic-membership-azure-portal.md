---
title: Azure Active Directory에서 특성 기반 동적 그룹 멤버 자격 | Microsoft Docs
description: 지원되는 식 규칙 연산자 및 매개 변수를 포함하는 동적 그룹 멤버 자격에 대한 고급 규칙을 만드는 방법.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/17/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 4eda67f9c28a52667a34af175086be19b627f2ce
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303371"
---
# <a name="create-attribute-based-rules-for-dynamic-group-membership-in-azure-active-directory"></a>Azure Active Directory에서 동적 그룹 멤버 자격에 대한 특성 기반 규칙 만들기
Azure AD(Azure Active Directory)에서 그룹에 대해 복잡한 특성 기반 동적 그룹 멤버 자격을 사용하도록 설정하기 위한 고급 규칙을 만들 수 있습니다. 이 문서는 특성 및 사용자 또는 장치에 대한 동적 멤버 자격 규칙을 만드는 구문에 대해 자세히 설명합니다. 보안 그룹 또는 Office 365 그룹에서 동적 멤버 자격에 대한 규칙을 설정할 수 있습니다.

사용자 또는 장치의 특성이 변경될 때 변경 내용이 그룹 추가 또는 제거를 트리거할지를 확인하기 위해 시스템은 디렉터리에서 모든 동적 그룹 규칙을 평가합니다. 사용자 또는 장치가 그룹에 대한 규칙을 만족하면 해당 그룹의 멤버로 추가됩니다. 규칙을 더 이상 만족하지 않는 경우 제거됩니다.

> [!NOTE]
> 이 기능은 하나 이상의 동적 그룹에 추가된 각 사용자 멤버에 대해 Azure AD Premium P1 라이선스가 필요합니다. 사용자가 동적 그룹의 구성원이 되도록 사용자에게 실제로 라이선스를 할당해야 하는 것은 아니지만 테넌트에 모든 사용자를 포괄할 수 있도록 최소한의 라이선스가 있어야 합니다. 예를 들어 테넌트의 모든 동적 그룹에 고유 사용자가 총 1,000명 있는 경우 라이선스 요구 사항을 충족하려면 Azure AD Premium P1에 대해 최소 1,000개 또는 그 이상의 라이선스가 있어야 합니다.
>
> 장치 또는 사용자에 대한 동적 그룹을 만들 수 있지만 사용자 및 장치 개체를 모두 포함하는 규칙을 만들 수는 없습니다.
> 
> 지금 사용자의 특성을 기반으로 하는 장치 그룹을 만들 수 없습니다. 장치 멤버 자격 규칙은 디렉터리에 있는 장치 개체의 즉각적인 특성만 참조할 수 있습니다.

## <a name="to-create-an-advanced-rule"></a>고급 규칙을 만들려면
1. 전역 관리자 또는 사용자 계정 관리자인 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. **사용자 및 그룹**을 선택합니다.
3. **모든 그룹**을 선택하고 **새 그룹**을 선택합니다.

   ![새 그룹 추가](./media/active-directory-groups-dynamic-membership-azure-portal/new-group-creation.png)

4. **그룹** 블레이드에서 새 그룹에 대한 이름 및 설명을 입력합니다. 사용자 또는 장치에 대한 규칙을 만들지 여부에 따라 **동적 사용자** 또는 **동적 장치** 중에서 **멤버 자격 유형**을 선택한 다음 **동적 쿼리 추가**를 선택합니다. 규칙 작성기를 사용하여 간단한 규칙을 작성하거나 고급 규칙을 직접 작성할 수 있습니다. 이 문서는 고급 규칙의 예제 뿐만 아니라 사용 가능한 사용자 및 장치 특성에 대한 자세한 정보를 포함합니다.

   ![동적 멤버 자격 규칙 추가](./media/active-directory-groups-dynamic-membership-azure-portal/add-dynamic-group-rule.png)

5. 규칙을 만든 후 블레이드 맨 아래에서 **쿼리 추가**를 선택합니다.
6. **만들기** on the **그룹**을 선택하여 그룹을 만듭니다.

> [!TIP]
> 입력한 고급 규칙이 올바르지 않으면 그룹 만들기가 실패할 수 있습니다. 포털의 오른쪽 상단 구석에 알림이 표시됩니다. 알림에는 시스템에서 규칙을 수락할 수 없는 이유에 대한 설명이 포함되어 있습니다. 이 알림을 신중하게 읽고 규칙을 유효하게 수정하는 방법을 이해하세요.

## <a name="constructing-the-body-of-an-advanced-rule"></a>고급 규칙 본문 생성
그룹의 동적 멤버 자격에 대해 만들 수 있는 고급 규칙은 기본적으로 세 부분으로 구성되며 true 또는 false 결과를 생성하는 이진 식입니다. 세 부분은 다음과 같습니다.

* 왼쪽 매개 변수
* 이항 연산자
* 오른쪽 상수

전체 고급 규칙은 (leftParameter binaryOperator "RightConstant")와 유사합니다. 여기서 열기 및 닫기 괄호는 전체 이진 식에 선택적이고, 큰 따옴표는 문자열인 오른쪽 상수에도 선택적이며 왼쪽 매개 변수의 구문은 user.property입니다. 고급 규칙은 -and, -or 및 -not 논리 연산자로 구분된 두 개 이상의 이진 식으로 구성될 수 있습니다.

다음은 제대로 구성된 고급 규칙의 예입니다.
```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```
지원되는 매개 변수 및 식 규칙 연산자의 전체 목록은 아래 섹션을 참조하세요. 장치 규칙에 사용되는 특성은 [특성을 사용하여 장치 개체에 대한 규칙 만들기](#using-attributes-to-create-rules-for-device-objects)를 참조하세요.

고급 규칙 본문의 총 길이는 2048자를 초과할 수 없습니다.

> [!NOTE]
> 문자열 및 regex 연산은 대/소문자를 구분하지 않습니다. *null*을 상수로 사용하여 Null 확인을 수행할 수도 있습니다(예: user.department -eq *null*).
> 따옴표(")를 포함하는 문자열은 ' 문자를 사용하여 이스케이프해야 합니다(예: user.department -eq \`"Sales").

## <a name="supported-expression-rule-operators"></a>지원되는 식 규칙 연산자
다음 표에는 지원되는 모든 식 규칙 연산자와 고급 규칙 본문에 사용할 수 있는 해당 구문이 나와 있습니다.

| 연산자 | 구문 |
| --- | --- |
| 같지 않음 |-ne |
| 같음 |-eq |
| 다음으로 시작 안 함 |-notStartsWith |
| 시작 |-startsWith |
| 포함하지 않음 |-notContains |
| contains |-contains |
| 일치하지 않음 |-notMatch |
| 일치 |-match |
| 그런 다음 | -in |
| 속하지 않음 | -notIn |

## <a name="operator-precedence"></a>연산자 우선 순위

모든 연산자는 낮은 우선 순위에서 높은 우선 순위로 아래에 나열됩니다. 같은 줄의 연산자는 같은 우선 순위에 있습니다.
````
-any -all
-or
-and
-not
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
````
모든 연산자는 하이픈(-) 접두사를 사용하거나 사용하지 않을 수 있습니다. 괄호는 우선 순위가 요구 사항을 충족하지 않을 경우에 필요합니다.
예: 
```
   user.department –eq "Marketing" –and user.country –eq "US"
```
이는 다음과 동등합니다.
```
   (user.department –eq "Marketing") –and (user.country –eq "US")
```
## <a name="using-the--in-and--notin-operators"></a>-In 및 -notIn 연산자 사용

사용자 특성의 값을 다양한 값과 비교하려면 -In 또는 -notIn 연산자를 사용할 수 있습니다. 다음은 -In 연산자를 사용하는 예제입니다.
```
    user.department -In [ "50001", "50002", "50003", “50005”, “50006”, “50007”, “50008”, “50016”, “50020”, “50024”, “50038”, “50039”, “51100” ]
```
값 목록의 처음과 끝에 "[" 및 "]"를 사용합니다. user.department 값이 목록의 값 중 하나와 같으면 이 조건에서 True로 평가합니다.


## <a name="query-error-remediation"></a>쿼리 오류 수정
다음 표에서는 일반적인 오류와 이를 수정하는 방법을 나열하고 있습니다.

| 쿼리 구문 분석 오류 | 잘못된 사용법 | 수정된 사용법 |
| --- | --- | --- |
| 오류: 특성이 지원되지 않습니다. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>[지원되는 속성 목록](#supported-properties)에 있는 특성인지 확인합니다. |
| 오류: 특성에서는 연산자가 지원되지 않습니다. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>사용된 연산자는 속성 형식에 지원되지 않습니다(이 예제에서 -contains는 부울 형식에 사용할 수 없습니다). 속성 형식에 올바른 연산자를 사용합니다. |
| 오류: 쿼리 컴파일 오류입니다. |1. (user.department -eq "Sales") (user.department -eq "Marketing")<br/><br/>2. (user.userPrincipalName -match "*@domain.ext") |1. 연산자가 없습니다. 두 개의 조인 조건자(-and 또는 -or)를 사용합니다.<br/><br/>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br/><br/>2. -match와 함께 사용되는 정규식 오류입니다.<br/><br/>(user.userPrincipalName -match ".*@domain.ext") 또는 (user.userPrincipalName -match "@domain.ext$")|

## <a name="supported-properties"></a>지원되는 속성
다음은 고급 규칙에 사용할 수 있는 모든 사용자 속성입니다.

### <a name="properties-of-type-boolean"></a>부울 형식의 속성
허용되는 연산자

* -eq
* -ne

| properties | 허용되는 값 | 사용 현황 |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>문자열 형식의 속성
허용되는 연산자

* -eq
* -ne
* -notStartsWith
* -startsWith
* -contains
* -notContains
* -match
* -notMatch
* -in
* -notIn

| properties | 허용되는 값 | 사용 현황 |
| --- | --- | --- |
| city |임의의 문자열 값 또는 *null*입니다. |(user.city -eq "value") |
| country |임의의 문자열 값 또는 *null*입니다. |(user.country -eq "value") |
| companyName | 임의의 문자열 값 또는 *null*입니다. | (user.companyName -eq "value") |
| department |임의의 문자열 값 또는 *null*입니다. |(user.department -eq "value") |
| displayName |임의의 문자열 값입니다. |(user.displayName -eq "value") |
| employeeId |임의의 문자열 값입니다. |(user.employeeId -eq "value")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |임의의 문자열 값 또는 *null*입니다. |(user.facsimileTelephoneNumber -eq "value") |
| givenName |임의의 문자열 값 또는 *null*입니다. |(user.givenName -eq "value") |
| jobTitle |임의의 문자열 값 또는 *null*입니다. |(user.jobTitle -eq "value") |
| mail |임의의 문자열 값 또는 *null*(사용자의 SMTP 주소)입니다. |(user.mail -eq "value") |
| mailNickName |임의의 문자열 값(사용자의 메일 별칭) |(user.mailNickName -eq "value") |
| mobile |임의의 문자열 값 또는 *null*입니다. |(user.mobile -eq "value") |
| objectId |사용자 개체의 GUID입니다. |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | 온-프레미스에서 클라우드로 동기화된 사용자의 온-프레미스 SID(보안 식별자)입니다. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |임의의 문자열 값 또는 *null*입니다. |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |임의의 문자열 값 또는 *null*입니다. |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 코드 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |임의의 문자열 값 또는 *null*입니다. |(user.sipProxyAddress -eq "value") |
| state |임의의 문자열 값 또는 *null*입니다. |(user.state -eq "value") |
| streetAddress |임의의 문자열 값 또는 *null*입니다. |(user.streetAddress -eq "value") |
| surname |임의의 문자열 값 또는 *null*입니다. |(user.surname-eq "value") |
| telephoneNumber |임의의 문자열 값 또는 *null*입니다. |(user.telephoneNumber -eq "value") |
| usageLocation |두 자로 된 국가 코드 |(user.usageLocation -eq "US") |
| userPrincipalName |임의의 문자열 값입니다. |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest *null* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>문자열 컬렉션 형식의 속성
허용되는 연산자

* -contains
* -notContains

| properties | 허용되는 값 | 사용 현황 |
| --- | --- | --- |
| otherMails |임의의 문자열 값입니다. |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

## <a name="multi-value-properties"></a>다중 값 속성
허용되는 연산자

* -any(컬렉션에서 적어도 하나의 항목이 조건과 일치하는 경우 충족)
* -all(컬렉션에서 모든 항목이 조건과 일치하는 경우 충족)

| properties | 값 | 사용 현황 |
| --- | --- | --- |
| assignedPlans |컬렉션에 있는 각 개체는 다음 문자열 속성을 표시합니다. capabilityStatus, service, servicePlanId |user.assignedPlans -any(assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |

다중 값 속성은 동일한 유형인 개체의 컬렉션입니다. -any 및 -all 연산자를 사용하여 각각 컬렉션의 항목 중 하나 또는 모두에 조건을 적용할 수 있습니다. 예: 

assignedPlans는 사용자에게 할당된 모든 서비스 계획을 나열하는 다중 값 속성입니다. 아래 식은 사용 상태인 Exchange Online(계획 2) 서비스 계획을 가진 사용자를 선택합니다.

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

(Guid 식별자는 Exchange Online(계획 2) 서비스 계획을 식별합니다.)

> [!NOTE]
> 예를 들어 특정 정책 집합이 포함된 대상으로 지정하는 데 Office 365(또는 기타 Microsoft 온라인 서비스) 기능을 사용하기 위해 모든 사용자를 식별하려는 경우에 유용합니다.

다음 식은 Intune 서비스("SCO" 서비스 이름으로 식별)와 연결된 서비스 계획이 있는 모든 사용자를 선택합니다.
```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

## <a name="use-of-null-values"></a>Null 값 사용

규칙에 null 값을 지정하려면 *null* 값을 사용할 수 있습니다. 단어 *null* 주위에 따옴표를 사용하지 않도록 주의합니다. 이렇게 하면 리터럴 문자열 값으로 해석됩니다. -not 연산자는 null에 대한 비교 연산자로 사용할 수 없습니다. 이 연산자를 사용할 경우 null을 사용하든 아니면 $null을 사용하든 오류가 발생합니다. 그 대신 -eq 또는 -ne를 사용하세요. null 값을 참조하는 올바른 방법은 다음과 같습니다.
```
   user.mail –ne $null
```

## <a name="extension-attributes-and-custom-attributes"></a>확장 특성 및 사용자 지정 특성
확장 특성 및 사용자 지정 특성은 동적 멤버 자격 규칙에서 지원됩니다.

확장 특성은 온-프레미스 Windows Server AD에서 동기화되고 "ExtensionAttributeX" 형식을 사용하며 여기서 X는 1 - 15입니다.
확장 특성을 사용하는 규칙의 예는 다음과 같습니다.
```
(user.extensionAttribute15 -eq "Marketing")
```
사용자 지정 특성은 온-프레미스 Windows Server AD 또는 연결된 SaaS 응용 프로그램에서 동기화되고 "user.extension_[GUID]\__[Attribute]" 형식입니다. 여기서 [GUID]는 AAD에서 특성을 만든 응용 프로그램에 대한 AAD의 고유한 식별자이고 [Attribute]는 만들어진 특성 이름입니다.
사용자 지정 특성을 사용하는 규칙의 예는 다음과 같습니다.
```
user.extension_c272a57b722d4eb29bfe327874ae79cb__OfficeNumber  
```
사용자 지정 특성 이름은 Graph Explorer를 사용하여 사용자의 특성을 쿼리하거나 특성 이름을 검색하여 디렉터리에서 찾을 수 있습니다.

## <a name="direct-reports-rule"></a>"직접 보고" 규칙
관리자의 직접 보고서를 모두 포함하는 그룹을 만들 수 있습니다. 관리자의 직접 보고서가 나중에 변경될 경우 그룹의 멤버 자격은 자동으로 조정됩니다.

> [!NOTE]
> 1. 규칙이 작동하려면 테넌트의 사용자에 대해 **관리자 ID** 속성이 올바르게 설정되어야 합니다. 해당 **프로필 탭**에서 사용자의 현재 값을 확인할 수 있습니다.
> 2. 이 규칙은 **직접** 보고서만을 지원합니다. 예를 들어 현재는 직접 보고서 및 해당 보고서를 포함하는 그룹과 같이 중첩된 계층에 그룹을 만들 수 없습니다.
> 3. 이 규칙은 다른 고급 규칙과 결합할 수 없습니다.

**그룹을 구성하려면**

1. [고급 규칙을 만들려면](#to-create-the-advanced-rule) 섹션의 1~5단계에 따르고 **동적 사용자**의 **멤버 자격 형식**을 선택합니다.
2. **동적 멤버 자격 규칙** 블레이드에서 다음 구문을 사용하여 규칙을 입력합니다.

    *"{objectID_of_manager}"의 직접 보고서*

    올바른 규칙의 예제:
```
                    Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```
    where “62e19b97-8b3d-4d4a-a106-4ce66896a863” is the objectID of the manager. The object ID can be found on manager's **Profile tab**.
3. 규칙을 저장한 후에 지정한 관리자 ID 값을 가진 모든 사용자가 그룹에 추가됩니다.

## <a name="using-attributes-to-create-rules-for-device-objects"></a>특성을 사용하여 장치 개체에 대한 규칙 만들기
또한 그룹의 멤버 자격에 대한 장치 개체를 선택하는 규칙을 만들 수 있습니다. 다음과 같은 장치 특성을 사용할 수 있습니다.

 장치 특성  | 값 | 예
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | 임의의 문자열 값입니다. |(device.displayName -eq "Rob Iphone”)
 deviceOSType | 임의의 문자열 값입니다. | (device.deviceOSType -eq "iPad") -또는 (device.deviceOSType -eq "iPhone")
 deviceOSVersion | 임의의 문자열 값입니다. | (device.OSVersion -eq "9.1")
 deviceCategory | 유효한 장치 범주 이름 | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | 임의의 문자열 값입니다. | (device.deviceManufacturer -eq "Samsung")
 deviceModel | 임의의 문자열 값입니다. | (device.deviceModel -eq "iPad Air")
 deviceOwnership | 개인, 회사, 알 수 없음 | (device.deviceOwnership -eq "Company")
 domainName | 임의의 문자열 값입니다. | (device.domainName -eq "contoso.com")
 enrollmentProfileName | Apple 장치 등록 프로필 또는 Windows Autopilot 프로필 이름 | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM(모바일 장치)<br>PC(Intune PC 에이전트에 의해 관리되는 컴퓨터) | (device.managementType -eq "MDM")
 organizationalUnit | 온-프레미스 Active Directory에 의해 설정된 조직 구성 단위의 이름과 일치하는 임의의 문자열 값 | (device.organizationalUnit -eq "US PCs")
 deviceId | 유효한 Azure AD 장치 ID | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | 유효한 Azure AD 개체 ID |  (device.objectId -eq 76ad43c9-32c5-45e8-a272-7b58b58f596d")



## <a name="changing-dynamic-membership-to-static-and-vice-versa"></a>동적 멤버 자격을 정적으로 또는 그 반대로 변경
그룹에서 멤버 자격을 관리하는 방식을 변경할 수 있습니다. 이것은 시스템에 동일한 그룹 이름과 ID를 유지하려는 경우에 유용하므로 그룹에 대한 기존 참조는 여전히 유효합니다. 새 그룹을 만들면 해당 참조를 업데이트해야 합니다.

이 기능 지원을 추가하도록 Azure AD 관리 센터를 업데이트했습니다. 이제 고객은 아래와 같이 Azure AD 관리 센터 또는 PowerShell cmdlet을 통해 기존 그룹을 동적 멤버 자격에서 할당된 멤버 자격으로 또는 그 반대로 변환할 수 있습니다.

> [!WARNING]
> 기존 정적 그룹을 동적 그룹으로 변경할 경우 기존의 모든 멤버가 그룹에서 제거된 후 멤버 자격 규칙이 처리되어 새 멤버가 추가됩니다. 그룹이 앱 또는 리소스에 대한 액세스를 제어하는 데 사용되는 경우 멤버 자격 규칙이 완전히 처리될 때까지는 원래 멤버가 액세스하지 못할 수 있습니다.
>
> 그룹의 새 멤버 자격이 예상대로 유지되는지 확인하기 위해 새 멤버 자격 규칙을 미리 테스트하는 것이 좋습니다.

### <a name="using-azure-ad-admin-center-to-change-membership-management-on-a-group"></a>Azure AD 관리 센터를 사용하여 그룹에 대한 멤버 자격 관리 변경 

1. 테넌트의 전역 관리자 또는 사용자 계정 관리자인 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. **그룹**을 선택합니다.
3. **모든 그룹** 목록에서 변경하려는 그룹을 엽니다.
4. **속성**을 선택합니다.
5. 해당 그룹의 **속성**에서, 원하는 멤버 자격 종류에 따라 **멤버 자격 유형**을 할당됨(정적), 동적 사용자 또는 동적 장치 중에 선택합니다. 동적 멤버 자격의 경우 규칙 작성기를 사용하여 간단한 규칙에 대한 옵션을 선택하거나 고급 규칙을 직접 작성할 수 있습니다. 

다음 단계는 사용자 무리에 대한 그룹을 정적 멤버 자격에서 동적 멤버 자격으로 변경하는 예제입니다. 

1. 선택한 그룹의**속성** 페이지에서 **멤버 자격 유형**을 **동적 사용자**로 선택한 다음, 그룹 멤버의 변경 내용을 설명하는 대화 상자에서 [예]를 선택하여 계속 진행합니다. 
  
   ![멤버 자격 유형을 동적 사용자로 선택](./media/active-directory-groups-dynamic-membership-azure-portal/select-group-to-convert.png)
  
2. **동적 쿼리 추가**를 선택하고 규칙을 입력합니다.
  
   ![규칙 입력](./media/active-directory-groups-dynamic-membership-azure-portal/enter-rule.png)
  
3. 규칙을 만든 후 페이지 맨 아래에서 **쿼리 추가**를 선택합니다.
4. 해당 그룹의 **속성** 페이지에서 **저장**을 선택하여 변경 내용을 저장합니다. 그룹 목록에서 해당 그룹의 **멤버 자격 유형**이 즉시 업데이트됩니다.

> [!TIP]
> 입력한 고급 규칙이 올바르지 않으면 그룹 변환이 실패할 수 있습니다. 포털의 오른쪽 상단 구석에 알림이 표시됩니다. 알림에는 시스템에서 규칙을 수락할 수 없는 이유에 대한 설명이 포함되어 있습니다. 이 알림을 신중하게 읽고 규칙을 올바르게 수정하는 방법을 이해하세요.

### <a name="using-powershell-to-change-membership-management-on-a-group"></a>PowerShell을 사용하여 그룹에 대한 멤버 자격 관리 변경

> [!NOTE]
> 동적 그룹 속성을 변경하려면 **Azure AD PowerShell 버전 2**의 [미리 보기 버전](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)에서 cmdlet을 사용해야 합니다. [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureADPreview)에서 미리 보기를 설치할 수 있습니다.

기존 그룹에서 멤버 자격 관리를 전환하는 기능의 예는 다음과 같습니다. 이 예제에서는 GroupTypes 속성을 정확하게 조작하고 동적 멤버 자격과 무관한 다른 기존 값을 그대로 유지하기 위해 주의가 필요합니다.

```
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
그룹을 고정으로 지정하려면:
```
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```
그룹을 동적으로 지정하려면:
```
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```
## <a name="next-steps"></a>다음 단계
이러한 문서는 Azure Active Directory의 그룹에 대한 추가 정보를 제공합니다.

* [기존 그룹 보기](active-directory-groups-view-azure-portal.md)
* [새 그룹을 만들고 멤버 추가](active-directory-groups-create-azure-portal.md)
* [그룹의 설정 관리](active-directory-groups-settings-azure-portal.md)
* [그룹의 멤버 자격 관리](active-directory-groups-membership-azure-portal.md)
* [그룹의 사용자에 대한 동적 규칙 관리](active-directory-groups-dynamic-membership-azure-portal.md)
