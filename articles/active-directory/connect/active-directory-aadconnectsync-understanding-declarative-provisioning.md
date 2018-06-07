---
title: "Azure AD Connect: 선언적 프로비전 이해 | Microsoft Docs"
description: "Azure AD connect에서 선언적 프로비전 구성 모델을 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 50fce526d667fa829551425edff4bd3863429ef2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect 동기화: 선언적 프로비전 이해
이 항목에서는 Azure AD Connect에서 구성 모델을 설명합니다. 이 모델은 선언적 프로비전이라고 하고 구성을 쉽게 변경할 수 있습니다. 이 항목에서 설명하는 여러 가지 항목은 고급이며 대부분의 고객 시나리오에 필요하지 않습니다.

## <a name="overview"></a>개요
선언적 프로비전은 연결된 원본 디렉터리에서 들어오는 개체를 처리하고 개체 및 특성이 원본에서 대상으로 변환되는 방법을 결정합니다. 개체는 동기화 파이프라인에서 처리되고 파이프라인은 인바운드 및 아웃 바운드 규칙에 동일합니다. 인바운드 규칙은 커넥터 공간에서 메타버스까지이고 아웃바운드 규칙은 메타버스에서 커넥터 공간까지입니다.

![파이프라인 동기화](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

파이프라인에는 여러 다른 모듈이 있습니다. 각 모듈은 동기화 개체에서 한 가지 개념을 담당합니다.

![파이프라인 동기화](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

* 원본, 원본 개체
* [범위](#scope), 범위에 있는 모든 동기화 규칙을 찾습니다.
* [조인](#join), 커넥터 공간과 메타버스 간의 관계를 결정합니다.
* [변환](#transform), 특성을 변환하는 방법 및 흐름을 계산합니다.
* [우선 순위](#precedence), 특성 기여의 충돌을 해결합니다.
* 대상, 대상 개체

## <a name="scope"></a>범위
범위 모듈은 개체를 평가하고 범위 내에서 처리에 포함되어야 하는 규칙을 결정합니다. 개체에 대한 특성 값에 따라 다른 동기화 규칙이 범위에 있다고 평가됩니다. 예를 들어 사서함이 없는 비활성화된 사용자에게는 사서함이 있는 활성화된 사용자보다 다양한 규칙이 적용됩니다.  
![범위](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

범위는 그룹 및 절로 정의됩니다. 절은 그룹 내에 있습니다. 논리적 AND는 그룹의 모든 절 간에 사용됩니다. 예를 들어 (부서 = IT AND 국가 = 덴마크). 논리 OR은 그룹 간에 사용됩니다.

![범위](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
이 그림의 범위는 (부서 = IT AND 국가 = 덴마크) OR (국가 = 스웨덴)으로 읽혀야 합니다. 그룹 1 또는 그룹 2가 true로 평가되는 경우 규칙은 범위 내에 있습니다.

범위 모듈은 다음 작업을 지원합니다.

| 작업 | 설명 |
| --- | --- |
| EQUAL, NOTEQUAL |값이 특성의 값과 같은지 평가하는 문자열 비교입니다. 다중값 특성의 경우 ISIN 및 ISNOTIN를 확인합니다. |
| LESSTHAN, LESSTHAN_OR_EQUAL |값이 특성의 값보다 적은지 평가하는 문자열 비교입니다. |
| CONTAINS, NOTCONTAINS |값이 특성의 값 내의 어딘가에 있을 수 있는지 평가하는 문자열 비교입니다. |
| STARTSWITH, NOTSTARTSWITH |값이 특성 값의 시작 부분에 있는지 평가하는 문자열 비교입니다. |
| ENDSWITH, NOTENDSWITH |값이 특성 값의 끝 부분에 있는지 평가하는 문자열 비교입니다. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |값이 특성의 값보다 큰지 평가하는 문자열 비교입니다. |
| ISNULL, ISNOTNULL |특성이 개체에서 없는지 평가합니다. 특성이 나타나지 않고 null인 경우 규칙은 범위 내에 있습니다. |
| ISIN, ISNOTIN |값이 정의된 특성에 있는지 평가합니다. 이 작업은 EQUAL 및 NOTEQUAL 다중값 변형입니다. 특성은 다중값 특성이어야 하고 값이 특성 값 중 하나에 있는 경우 규칙은 범위 내에 있습니다. |
| ISBITSET, ISNOTBITSET |특정 비트가 설정되었는지 평가합니다. 예를 들어 사용자가 활성화되었는지 비활성화되었는지를 확인하기 위해 userAccountControl에서 비트를 평가하는 데 사용할 수 있습니다. |
| ISMEMBEROF, ISNOTMEMBEROF |값은 커넥터 공간의 그룹에서 DN을 포함해야 합니다. 개체가 지정된 그룹의 멤버인 경우 규칙은 범위 내에 있습니다. |

## <a name="join"></a>Join
동기화 파이프라인에서 조인 모듈은 원본의 개체와 대상의 개체 간의 관계를 찾는 작업을 담당합니다. 인바운드 규칙에서 이 관계는 메타버스의 개체에 대한 관계를 찾는 커넥터 공간의 개체일 수 있습니다.  
![cs와 mv 간의 조인](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
목표는 다른 커넥터에서 만든 메타버스에 이미 개체가 있는지 확인하는 것입니다. 이는 연결되어야 합니다. 예를 들어 계정-리소스 포리스트에서 계정 포리스트의 사용자는 리소스 포리스트의 사용자와 조인되어야 합니다.

조인은 대부분 인바운드 규칙에서 커넥터 공간 개체를 동일한 메타버스 개체에 조인하는 데 사용됩니다.

조인은 하나 이상의 그룹으로 정의됩니다. 그룹 내에 절이 있습니다. 논리적 AND는 그룹의 모든 절 간에 사용됩니다. 논리 OR은 그룹 간에 사용됩니다. 그룹은 위에서 아래의 순서로 처리됩니다. 하나의 그룹이 대상의 개체와 정확히 일치하는 하나의 개체를 찾는 경우 다른 조인 규칙은 평가되지 않습니다. 0개 이상의 개체를 찾을 수 있는 경우 처리는 규칙의 다음 그룹으로 계속됩니다. 이러한 이유로 규칙은 가장 명시적인 것이 앞서고 더 유사한 것이 끝으로 보내는 순서대로 만들어져야 합니다.  
![조인 정의](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
이 그림에서 조인은 위쪽에서 아래쪽으로 처리됩니다. 먼저 동기화 파이프라인은 employeeID에 대해 일치하는지 확인합니다. 그렇지 않으면 두 번째 규칙은 계정 이름이 객체를 조인하는 데 사용되는지 확인합니다. 일치하지 않는 경우 세 번째이자 마지막 규칙은 사용자의 이름을 사용하여 더 유사한 일치를 확인합니다.

모든 조인 규칙이 평가되며 일치 항목이 없는 경우 **설명** 페이지의 **링크 형식**이 사용됩니다. 이 옵션이 **프로비전**에 설정된 경우 대상에서 새 개체가 만들어집니다.  
![프로비전 또는 조인](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

개체에는 범위 내에 조인 규칙이 포함된 하나의 단일 동기화 규칙이 있어야 합니다. 조인이 정의되는 여러 동기화 규칙이 있는 경우 오류가 발생합니다. 우선 순위는 조인 충돌을 해결하는 데 사용되지 않습니다. 개체는 동일한 인바운드/아웃바운드 방향을 갖는 흐름에 대한 특성의 범위에 조인 규칙을 가지고 있어야 합니다. 동일한 개체에 대한 인바운드 및 아웃바운드 모든 특성이 흘러야 하는 경우 조인된 인바운드 및 아웃바운드 동기화 규칙이 모두 있어야 합니다.

대상 커넥터 공간에 개체를 프로비전하려고 할 때 아웃바운드 조인에는 특별한 동작이 포함됩니다. DN 특성은 먼저 역방향 조인을 시도하는 데 사용됩니다. 동일한 DN을 가진 대상 커넥터 공간에 개체가 있는 경우 이 개체는 조인됩니다.

범위에 새 동기화 규칙을 가져온 경우 조인 모듈은 한 번만 평가됩니다. 개체가 조인하는 경우 조인 조건을 더 이상 충족하지 않는 경우에도 조인이 해제되지 않습니다. 개체의 조인을 해제하려면 개체에 조인된 동기화 규칙은 범위에서 벗어나야 합니다.

### <a name="metaverse-delete"></a>메타버스 삭제
하나의 동기화 규칙이 **프로비전** 또는 **StickyJoin**으로 설정된 **링크 유형**과 함께 범위에 있는 한 메타버스 개체는 유지됩니다. 커넥터가 메타버스에 새 개체를 프로비전하도록 허용되지 않으면 StickyJoin을 사용합니다. 하지만 해당 항목이 조인되는 경우 메타버스 개체가 삭제되기 전에 원본에서 삭제되어야 합니다.

메타버스 개체가 삭제되면 **프로비전** 되도록 표시된 아웃바운드 동기화 규칙과 연결된 모든 개체는 삭제되도록 표시됩니다.

## <a name="transformations"></a>변환
변환은 특성이 원본에서 대상에 흐르는 방식을 정의하는 데 사용됩니다. 흐름은 직접, 상수 또는 식과 같은 **흐름 유형**중 하나를 가질 수 있습니다. 직접 흐름은 추가 변환 없이 그대로 특성 값을 사용합니다. 상수 값은 지정된 값을 설정합니다. 식은 선언적 프로비전 식 언어를 사용하여 변환이 어떻게 되어야 하는지를 표현합니다. 식 언어에 대한 세부 정보는 [선언적 프로비전 식 언어 이해](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) 항목에서 찾을 수 있습니다.

![프로비전 또는 조인](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

**한 번 적용** 확인란은 개체를 처음 만들 때에만 특성을 설정해야 한다고 정의합니다. 예를 들어, 이 구성을 사용하여 새 사용자 개체에 대한 초기 암호를 설정할 수 있습니다.

### <a name="merging-attribute-values"></a>특성 값 병합
특성 흐름에는 여러 다른 커넥터에서 다중값 특성을 병합해야 하는지를 결정하는 설정이 있습니다. 기본값은 **업데이트**이며 우선 순위가 높은 동기화 규칙이 우선함을 나타냅니다.

![형식 병합](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

또한 **병합** 및 **MergeCaseInsensitive**가 있습니다. 이 옵션을 사용하면 다양한 원본에서 값을 병합할 수 있습니다. 예를 들어, 여러 다른 포리스트에서 구성원 또는 proxyAddresses 특성을 병합하는 데 사용할 수 있습니다. 이 옵션을 사용하는 경우 하나의 개체의 범위에 있는 모든 동기화 규칙은 동일한 병합 유형을 사용해야 합니다. 한 커넥터의 **업데이트** 및 다른 커넥터의 **병합**을 정의할 수는 없습니다. 시도하면 오류가 발생합니다.

**병합** 및 **MergeCaseInsensitive** 간에는 중복 특성 값을 처리하는 방법에 차이가 있습니다. 동기화 엔진은 중복 값이 대상 특성에 삽입되지 않도록 합니다. **MergeCaseInsensitive**와 차이가 있는 경우 중복 값이 표시되지 않습니다. 예를 들어, 대상 특성에 SMTP:bob@contoso.com 및 smtp:bob@contoso.com이 모두 표시되지 않습니다. **병합** 은 정확한 값을 확인하고 차이가 있는 경우 다중값이 표시되지 않을 수 있습니다.

**대체** 옵션은 **업데이트**와 동일하지만 사용되지 않습니다.

### <a name="control-the-attribute-flow-process"></a>특성 흐름 프로세스 제어
동일한 메타 버스 특성에 적용하도록 인바운드 동기화 규칙을 여러 개 구성한 경우, 우선 순위는 우선권을 결정하는 데 사용됩니다. 가장 높은 우선 순위(가장 낮은 숫자 값)의 동기화 규칙이 값이 적용됩니다. 이는 아웃바운드 규칙에 대해서도 동일하게 적용됩니다. 가장 높은 우선 순위의 동기화 규칙이 우선하고 연결된 된 디렉터리에 값을 적용합니다.

경우에 따라 동기화 규칙은 값을 적용하는 대신 다른 규칙의 적용 방식을 결정합니다. 그러한 경우에 사용되는 특수한 리터럴이 몇 가지 있습니다.

인바운드 동기화 규칙의 경우, **NULL** 리터럴은 흐름이 어떤 값도 적용하지 않음을 나타내는 데 사용할 수 있습니다. 우선 순위가 낮은 다른 규칙이 값을 적용할 수 있습니다. 값을 기여한 규칙이 없다면 메타 버스 특성이 제거됩니다. 아웃바운드 규칙에서 모든 동기화 규칙이 처리된 후 최종 값이 **NULL** 인 경우 해당 값은 연결된 디렉터리에서 제거됩니다.

**AuthoritativeNull** 리터럴은 **NULL**과 비슷하지만, 차이점은 낮은 우선 순위 규칙이 값을 적용할 수 없다는 것입니다.

특성 흐름은 또한 **IgnoreThisFlow**를 사용할 수 있습니다. 이는 적용할 것이 없다는 것을 나타내는 면에서 NULL과 유사합니다. 차이점은 대상에서 이전의 기존 값을 제거하지 않는다는 것입니다. 이는 특성 흐름이 애초에 있지 않았다는 것과 같습니다.

다음은 예제입니다.

*AD로 나가기 - 사용자 Exchange 하이브리드*에서 다음 흐름을 찾을 수 있습니다.  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
이 식은 다음과 같이 읽어야 합니다. 사용자 사서함이 Azure AD에 있는 경우 특성을 Azure AD에서 AD로 전달합니다. 그렇지 않으면 Active Directory에 아무 것도 전달되지 않습니다. 이 경우 AD의 기존 값이 유지됩니다.

### <a name="importedvalue"></a>ImportedValue
ImportedValue 함수는 특성 이름을 대괄호 대신 따옴표로 묶어야 하므로 다른 모든 함수와 다릅니다.  
`ImportedValue("proxyAddresses")`

일반적으로 동기화 중에는 내보내는 동안("타워 최상위")에 오류가 발생하거나 아직 내보내지 않았어도 특성이 예상 값을 사용합니다. 인바운드 동기화는 연결된 디렉터리에 아직 도달하지 않은 특성도 결국 도달할 것으로 가정합니다. 일부 경우에는 연결된 디렉터리에서 확인한 값만 동기화한다는 것이 중요합니다("홀로그램 및 델타 가져오기 타워").

기본 동기화 규칙 *AD에서 들어오기 – Exchange에서 사용자 공통*에서 이 함수의 예제를 찾을 수 있습니다. 하이브리드 Exchange에서 값을 성공적으로 내보냈음이 확인된 경우 Exchange Online에 의해 추가되는 값만 동기화됩니다.  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>우선 순위
여러 동기화 규칙에서 동일한 특성 값을 대상에 적용하려고 시도할 경우 우선 순위 값은 승자를 결정하는 데 사용됩니다. 가장 높은 우선 순위인 가장 낮은 숫자 값을 가진 규칙이 충돌하는 특성을 적용합니다.

![형식 병합](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

이 순서 지정은 개체의 작은 하위 집합에 대한 보다 정확한 특성 흐름을 정의하는 데 사용될 수 있습니다. 예를 들어, Windows에서 제공하지 않는 규칙은 활성화된 계정(**User AccountEnabled**)의 특성이 다른 계정보다 우선 순위를 갖도록 합니다.

커넥터 간에 우선 순위를 정의할 수 있습니다. 더 좋은 데이터를 가진 커넥터가 값을 먼저 적용할 수 있습니다.

### <a name="multiple-objects-from-the-same-connector-space"></a>동일한 커넥터 공간의 여러 개체
여러 개체가 동일한 메타버스 개체에 조인된 동일한 커넥터 공간에 있는 경우 우선 순위를 조정해야 합니다. 여러 개체가 동일한 동기화 규칙의 범위에 있는 경우 동기화 엔진은 우선 순위를 결정할 수 없습니다. 어떤 원본 개체가 메타버스에 값을 제공해야 하는지 모호합니다. 원본에 있는 특성이 동일한 값인 경우더라도 이 구성은 모호하다고 보고됩니다.  
![동일한 mv 개체에 조인된 여러 개체](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

이 시나리오의 경우 원본 개체에 범위의 여러 동기화 규칙이 있도록 동기화 규칙의 범위를 변경해야 합니다. 다른 우선 순위를 정의할 수 있습니다.  
![동일한 mv 개체에 조인된 여러 개체](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>다음 단계
* [선언적 프로비전 식 이해](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)에서 식 언어에 대해 자세히 알아봅니다.
* [기본 구성 이해](active-directory-aadconnectsync-understanding-default-configuration.md)에서 선언적 프로비전이 기본으로 사용되는 방법을 참조하세요.
* [기본 구성으로 변경하는 방법](active-directory-aadconnectsync-change-the-configuration.md)에서 선언적 프로비전을 사용하여 실용적으로 변경하는 방법을 참조하세요.
* [사용자 및 연락처 이해](active-directory-aadconnectsync-understanding-users-and-contacts.md)에서 사용자 및 연락처를 함께 작동하는 방법을 계속해서 알아봅니다.

**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

**참조 항목**

* [Azure AD 동기화 연결: 함수 참조](active-directory-aadconnectsync-functions-reference.md)
