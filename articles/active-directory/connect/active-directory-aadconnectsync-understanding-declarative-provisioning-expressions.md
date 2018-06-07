---
title: 'Azure AD Connect: 선언적 프로비전 식 | Microsoft Docs'
description: 선언적 프로비전 식에 대해 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 797c0949aceea415652a72df5ee23ef9888ab975
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect 동기화: 선언적 프로비전 식 이해
Azure AD Connect 동기화는 Forefront Identity Manager 2010에 처음 도입된 선언적 프로비전을 기반으로 합니다. 컴파일된 코드를 작성할 필요 없이 전체 ID 통합 비즈니스 논리를 구현할 수 있습니다.

선언적 프로비전의 핵심적인 부분은 특성 흐름에 사용되는 표현 언어입니다. 사용 되는 언어는 VBA(Microsoft® Visual Basic® for Applications)의 하위 집합입니다. 이 언어는 Microsoft Office에서 사용되며, VBScript 경험이 있는 사용자 또한 이 언어를 인식합니다. 선언적 프로비전 표현 언어는 함수만 사용하며 구조적 언어는 아닙니다. 메서드 또는 문이 없습니다. 대신, 빠른 프로그램 흐름에 함수가 중첩됩니다.

자세한 내용은 [Office 2013용 Visual Basic for Applications 언어 참조 시작](https://msdn.microsoft.com/library/gg264383.aspx)을 참조하세요.

특성은 강력한 형식입니다. 함수는 올바른 형식의 특성만 허용합니다. 대/소문자를 구분하기도 합니다. 함수 이름과 특성 이름은 모두 적절한 대/소문자를 가지고 있어야 하며 그렇지 않으면 오류가 발생합니다.

## <a name="language-definitions-and-identifiers"></a>언어 정의 및 식별자
* 함수는 다음과 같이 이름 뒤에 대괄호로 인수가 붙습니다. FunctionName(인수 1, 인수 N).
* 특성은 다음과 같이 대괄호로 식별됩니다. [attributeName]
* 매개 변수는 다음과 같이 백분율 기호로 식별됩니다. % ParameterName %
* 문자열 상수는 따옴표를 사용합니다(예: "Contoso"). (참고: 둥근 따옴표 “”가 아닌 직선 따옴표 ""를 사용)
* 숫자 값은 따옴표 없이 표현되고 10진수입니다. 16진수 값은 접두사 &H가 붙습니다. 예: 98052, &HFF
* 부울 값은 다음과 같은 상수로 표시됩니다. True, False
* 기본 제공 상수 및 리터럴은 자신의 이름으로만 표현됩니다. NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
선언적 프로비전은 여러 함수를 사용하여 특성 값을 변환할 수 있도록 합니다. 함수의 결과가 다른 함수로 전달되도록 이러한 함수는 중첩될 수 있습니다.

`Function1(Function2(Function3()))`

전체 함수 목록은 [함수 참조](active-directory-aadconnectsync-functions-reference.md)에서 확인할 수 있습니다.

### <a name="parameters"></a>매개 변수
매개 변수는 커넥터를 통해 또는 PowerShell을 사용하는 관리자에 의해 정의됩니다. 매개 변수는 일반적으로 시스템별로 다른 값을 포함합니다(예: 사용자가 위치한 도메인 이름). 이러한 매개 변수를 특성 흐름에서 사용할 수 있습니다.

Active Directory Connector는 인바운드 동기화 규칙에 대해 다음 매개 변수를 제공합니다.

| 매개 변수 이름 | 주석 |
| --- | --- |
| Domain.Netbios |현재 가져오는 도메인의 Netbios 형식(예: FABRIKAMSALES) |
| Domain.FQDN |현재 가져오는 도메인의 FQDN 형식(예: sales.fabrikam.com) |
| Domain.LDAP |현재 가져오는 도메인의 LDAP 형식(예: DC=sales,DC=fabrikam,DC=com) |
| Forest.Netbios |현재 가져오는 포리스트 이름의 Netbios 형식(예: FABRIKAMCORP) |
| Forest.FQDN |현재 가져오는 포리스트 이름의 FQDN 형식(예: fabrikam.com) |
| Forest.LDAP |현재 가져오는 포리스트 이름의 LDAP 형식(예: DC=fabrikam,DC=com) |

시스템은 현재 실행 중인 커넥터의 ID를 가져오는 데 사용되는 다음 매개 변수를 제공합니다.  
`Connector.ID`

사용자가 위치한 도메인의 netbios 이름이 있는 메타버스 특성 도메인으로 채워지는 예제는 다음과 같습니다.  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>연산자
다음과 같은 연산자를 사용할 수 있습니다.

* **비교**: <, <=, <>, =, >, >=
* **수학**: +, -, \*, -
* **문자열**: &(연결)
* **논리**: &&(및), ||(또는)
* **계산 순서**: ( )

연산자는 왼쪽에서 오른쪽으로 계산되며 계산 우선 순위가 같습니다. 즉, \*(승수)는 -(빼기) 전에 계산되지 않습니다. 2\*(5+3)은 2\*5+3과 같지 않습니다. 대괄호 ()는 왼쪽에서 오른쪽 계산 순서가 적절하지 않을 때 계산 순서를 변경하는 데 사용됩니다.

## <a name="multi-valued-attributes"></a>다중값 특성
함수는 단일 값 및 다중값 특성에서 작동할 수 있습니다. 다중값 특성의 경우 함수는 모든 값에 대해 작동하고 각 값에 동일한 함수를 적용합니다.

예를 들어   
`Trim([proxyAddresses])` proxyAddress 특성의 모든 값에 Trim을 수행합니다.  
`Word([proxyAddresses],1,"@") & "@contoso.com"` @-sign이 있는 모든 값의 경우 도메인을 @contoso.com으로 바꿉니다.  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])` SIP 주소를 찾아서 값을 제거합니다.

## <a name="next-steps"></a>다음 단계
* [선언적 프로비전 이해](active-directory-aadconnectsync-understanding-declarative-provisioning.md)에서 구성 모델에 대해 자세히 알아봅니다.
* [기본 구성 이해](active-directory-aadconnectsync-understanding-default-configuration.md)에서 선언적 프로비전이 기본으로 사용되는 방법을 참조하세요.
* [기본 구성으로 변경하는 방법](active-directory-aadconnectsync-change-the-configuration.md)에서 선언적 프로비전을 사용하여 실용적으로 변경하는 방법을 참조하세요.

**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

**참조 항목**

* [Azure AD 동기화 연결: 함수 참조](active-directory-aadconnectsync-functions-reference.md)

