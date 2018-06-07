---
title: Azure Active Directory 감사 API 참조 | Microsoft Docs
description: Azure Active Directory 감사 API를 시작하는 방법
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: e620a7f488e51a60bff6943135831eea0d12816d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34158050"
---
# <a name="azure-active-directory-audit-api-reference"></a>Azure Active Directory 감사 API 참조

> [!TIP] 
> 결국에는 이 API를 대체하게 되는 [보고](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)를 위한 새 Microsoft Graph API를 확인해 보세요. 


이 문서는 Azure AD(Azure Active Directory) 보고 API에 대한 문서 모음의 일부입니다. Azure AD Reporting은 코드 또는 관련된 도구를 사용하여 감사 데이터에 액세스할 수 있는 API를 제공합니다.
이 문서의 범위는 **감사 API**에 대한 참조 정보를 제공하는 것입니다.

다음을 참조하세요.

* 자세한 개념 정보는 [감사 로그](active-directory-reporting-azure-portal.md#activity-reports)를 참조하세요.

* [Azure Active Directory Reporting API 시작](active-directory-reporting-api-getting-started.md) 을 참조하세요.


관련 작업:

- 질문과 대답의 경우 [FAQ](active-directory-reporting-faq.md)를 참조하세요. 

- 문제의 경우 [지원 티켓을 작성하세요.](active-directory-troubleshooting-support-howto.md) 


## <a name="who-can-access-the-data"></a>데이터에 액세스할 수 있는 사용자는 누구인가요?
* 보안 관리 또는 보안 판독기 역할의 사용자
* 전역 관리자
* API에 액세스할 권한이 있는 모든 앱(앱 권한 부여는 전역 관리자의 권한에 따라 설정할 수 있음)

## <a name="prerequisites"></a>필수 조건
보고 API를 통해 이 보고서에 액세스하려면 다음이 있어야 합니다.

* [Azure Active Directory 무료 이상 버전](active-directory-whatis.md)
* [Azure AD Reporting API에 액세스하기 위한 필수 구성 요소](active-directory-reporting-api-prerequisites.md)를 완료합니다. 

## <a name="accessing-the-api"></a>API에 액세스
예를 들어 [Graph Explorer](https://graphexplorer2.cloudapp.net) 를 통해 또는 프로그래밍 방식으로 PowerShell을 사용하여 이 API에 액세스할 수 있습니다. PowerShell에서 AAD Graph REST 호출에 사용된 OData 필터 구문을 해석할 수 있도록 하려면 '(일명 억음 악센트 기호) 문자를 사용하여 $ 문자를 "이스케이프"합니다. ' 문자는 [PowerShell의 이스케이프 문자](https://technet.microsoft.com/library/hh847755.aspx)로 사용되어 PowerShell에서 $ 문자의 리터럴 해석을 수행하고 PowerShell 변수 이름(예: $filter)으로 혼동하지 않도록 방지합니다.

이 문서에서는 Graph 탐색기에 집중합니다. PowerShell 예제는 이 [PowerShell 스크립트](active-directory-reporting-api-audit-samples.md#powershell-script)를 참조하세요.

## <a name="api-endpoint"></a>API 끝점

다음 URI를 사용하여 이 API에 액세스할 수 있습니다.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Azure AD 감사 API에서 OData 페이지 매김을 사용하여 반환되는 레코드의 수는 제한되지 않습니다. 보고서 데이터 보존에 대한 제한 사항은 [보고서 보존 정책](active-directory-reporting-retention.md)을 참조하세요.

호출에서는 데이터를 일괄적으로 반환합니다. 각 배치에는 최대 1000개 레코드가 있습니다. 레코드의 다음 일괄 처리를 가져오려면 **다음** 링크를 사용합니다. 반환된 첫 번째 레코드 집합에서 건너뛰기 토큰 정보를 가져옵니다. 건너뛰기 토큰은 결과 집합의 끝에 있습니다.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>지원되는 필터

필터를 사용하여 API 호출에서 반환되는 레코드의 수를 줄일 수 있습니다.  
로그인 API 관련 데이터에 지원되는 필터는 다음과 같습니다.

* **$top=\<반환될 레코드의 수\>** - 반환된 레코드의 수를 제한합니다. 이 작업은 비용이 많이 드는 작업입니다. 수천 개의 개체를 반환하려는 경우 이 필터는 사용하지 마세요.     
* **$filter=\<필터 문\>** - 지원되는 필터 필드를 기반으로 원하는 레코드 종류를 지정합니다.

## <a name="supported-filter-fields-and-operators"></a>지원되는 필터 필드 및 연산자
원하는 레코드 종류를 지정하려면 다음 필터 필드 중 하나 이상이 포함된 필터 문을 작성할 수 있습니다.

* [activityDate](#activitydate) - 날짜 또는 날짜 범위를 정의합니다.
* [범주](#category) - 필터링 기준으로 사용할 범주를 정의합니다.
* [activityStatus](#activitystatus) - 활동의 상태를 정의합니다.
* [activityType](#activitytype) - 동작의 종류를 정의합니다.
* [활동](#activity) - 활동을 문자열로 정의합니다.  
* [행위자/이름](#actorname) - 행위자 이름의 형태로 행위자를 정의합니다.
* [행위자/objectid](#actorobjectid) - 행위자 ID의 형태로 행위자를 정의합니다.   
* [행위자/upn](#actorupn) - 행위자 UPN(사용자 계정 이름)의 형태로 행위자를 정의합니다. 
* [대상/이름](#targetname) - 행위자 이름의 형태로 대상을 정의합니다.
* [대상/objectid](#targetobjectid) - 행위자 ID의 형태로 대상을 정의합니다.  
* [대상/upn](#targetupn) - 행위자 UPN(사용자 계정 이름)의 형태로 행위자를 정의합니다.   

- - -
### <a name="activitydate"></a>activityDate
**지원되는 연산자**: eq, ge, le, gt, lt

**예제**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago    

**참고**:

datetime는 UTC 형식이어야 합니다.

- - -
### <a name="category"></a>카테고리

**지원되는 값**:

| Category                         | 값     |
| :--                              | ---       |
| 핵심 디렉터리                   | 디렉터리 |
| 셀프 서비스 암호 관리 | SSPR      |
| 셀프 서비스 그룹 관리    | SSGM      |
| 계정 프로비전             | 동기화      |
| 자동화된 암호 롤오버      | 자동화된 암호 롤오버 |
| ID 보호              | IdentityProtection |
| 사용자 초대                    | 사용자 초대 |
| MIM 서비스                      | MIM 서비스 |



**지원되는 연산자**: eq

**예제**:

    $filter=category eq 'SSPR'
- - -
### <a name="activitystatus"></a>activityStatus

**지원되는 값**:

| 활동 상태 | 값 |
| :--             | ---   |
| 성공         | 0     |
| 실패         | - 1   |

**지원되는 연산자**: eq

**예제**:

    $filter=activityStatus eq -1    

---
### <a name="activitytype"></a>activityType
**지원되는 연산자**: eq

**예제**:

    $filter=activityType eq 'User'    

**참고**:

대/소문자 구분

- - -
### <a name="activity"></a>활동
**연산자 지원**: eq, contains, startsWith

**예제**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')    

**참고**:

대/소문자 구분

- - -
### <a name="actorname"></a>행위자/이름
**연산자 지원**: eq, contains, startsWith

**예제**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')    

**참고**:

대/소문자 구분하지 않음

- - -
### <a name="actorobjectid"></a>행위자/objectid
**지원되는 연산자**: eq

**예제**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    


- - -
### <a name="targetname"></a>대상/이름
**연산자 지원**: eq, contains, startsWith

**예제**:

    $filter=targets/any(t: t/name eq 'some name')    

**참고**:

대/소문자 구분하지 않음

- - -
### <a name="targetupn"></a>대상/upn
**연산자 지원**: eq, startsWith

**예제**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**참고**:

* 대/소문자 구분하지 않음
* Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity를 쿼리할 때 전체 네임스페이스를 추가합니다.

- - -
### <a name="targetobjectid"></a>대상/objectid
**지원되는 연산자**: eq

**예제**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actorupn"></a>행위자/upn
**연산자 지원**: eq, startsWith

**예제**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')    

**참고**:

* 대/소문자 구분하지 않음 
* Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity를 쿼리할 때 전체 네임스페이스를 추가합니다.

- - -
## <a name="next-steps"></a>다음 단계

- 필터링된 시스템 활동에 대한 예제를 참조하시겠습니까? [Azure Active Directory 감사 API 샘플](active-directory-reporting-api-audit-samples.md)을 확인하세요.

- Azure AD Reporting API에 대해 자세히 살펴보시겠습니까? [Azure Active Directory Reporting API 시작](active-directory-reporting-api-getting-started.md)을 참조하세요.

