---
title: "ID 동기화 및 중복 특성 복원력 | Microsoft Docs"
description: "Azure AD Connect를 사용하여 디렉터리 동기화 동안 UPN 또는 ProxyAddress 충돌을 사용하여 개체를 처리하는 방법의 새 동작입니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.openlocfilehash: 975abed469a78573553c0879b33181d2a58ec48c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>ID 동기화 및 중복 특성 복원력
중복 특성 복원력은 Microsoft의 동기화 도구 중 하나를 실행하는 경우 **UserPrincipalName** 및 **ProxyAddress**의 충돌로 발생하는 마찰을 제거하는 Azure Active Directory의 기능입니다.

이 두 특성은 일반적으로 지정된 Azure Active Directory 테넌트의 모든 **사용자**, **그룹** 또는 **연락처** 개체에 고유해야 합니다.

> [!NOTE]
> 사용자만 UPN을 가질 수 있습니다.
> 
> 

이 기능이 활성화하는 새 동작은 동기화 파이프라인의 클라우드 부분에 있으므로 클라이언트와 관계 없으며 Azure AD Connect, DirSync 및 MIM + 커넥터를 포함하는 모든 Microsoft 동기화 제품과 관련되어 있습니다. 일반 용어인 "동기화 클라이언트"는 이 문서에서 이러한 제품 중 하나를 나타내는 데 사용됩니다.

## <a name="current-behavior"></a>현재 동작
이 고유성 제약 조건을 위반하는 UPN 또는 ProxyAddress 값으로 새 개체를 프로비전하려고 하는 경우 Azure Active Directory는 개체가 생성되는 것을 차단합니다. 마찬가지로 개체가 고유하지 않은 UPN 또는 ProxyAddress로 업데이트되면 업데이트가 실패합니다. 프로비전 시도 또는 업데이트는 각 내보내기 주기 시 동기화 클라이언트에서 다시 시도되고 충돌이 해결될 때까지 계속해서 실패합니다. 각 시도 시 오류 보고서가 발생되고 동기화 클라이언트에서 오류가 기록됩니다.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>중복 특성 복원력으로 동작
중복 특성으로 개체 프로비전 또는 업데이트에 완전히 실패하는 대신 Azure Active Directory는 고유성 제약 조건을 위반하는 중복 특성을 “격리합니다”. 이 특성이 UserPrincipalName과 같은 프로비전에 필요한 경우 서비스는 자리 표시자 값을 할당합니다. 이러한 임시 값의 형식은  
“***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>.onmicrosoft.com***”.  
**ProxyAddress**와 같은 특성이 필요하지 않은 경우 Azure Active Directory는 단순히 충돌 특성을 격리하고 개체 생성 또는 업데이트를 진행합니다.

특성을 격리 시 충돌에 대한 정보는 이전 동작에 사용된 동일한 오류 보고서 전자 메일에 전송됩니다. 그러나 격리가 발생할 때 이 정보는 오류 보고서에 한 번만 표시되며 이후 메일에 계속해서 기록되지 않습니다. 또한 이 개체에 대한 내보내기가 성공했으므로 동기화 클라이언트는 오류를 기록하지 않고 후속 동기화 주기 시 만들기 / 업데이트 작업을 시도하지 않습니다.

이 동작을 지원하기 위해 사용자, 그룹 및 연락처 개체 클래스:   
**DirSyncProvisioningError**

정상적으로 추가해야 하는 고유성 제약 조건을 위반하는 충돌 특성을 저장하는 데 사용되는 다중값 특성입니다. 해결된 중복 특성 충돌을 찾기 위해 1시간마다 실행되고 격리에서 문제의 특성을 자동으로 제거하는 백그라운드 타이머 작업이 Azure Active Directory에서 활성화되었습니다.

### <a name="enabling-duplicate-attribute-resiliency"></a>중복 특성 복원력 활성화
중복 특성 복원력은 모든 Azure Active Directory 테넌트의 새로운 기본 동작입니다. 2016년 8월 22일 또는 그 이후에 동기화를 처음으로 사용하는 모든 테넌트에 기본적으로 설정됩니다. 이 날짜 이전에 동기화를 사용하도록 설정한 테넌트에는 이 기능이 일괄적으로 사용하도록 설정됩니다. 이 출시는 2016년 9월에 시작되며 기능이 사용되는 특정 날짜와 함께 각 테넌트의 기술 알림 담당자에게 전자 메일 알림이 전송됩니다.

> [!NOTE]
> 중복 특성 복원력이 설정된 후에는 해제할 수 없습니다.

이 기능이 테넌트에 사용되는지 확인하려면 Azure Active Directory PowerShell 모듈의 최신 버전을 다운로드하여 다음을 실행하면 됩니다.

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> 이제는 중복 특성 복원력 기능을 사용자 테넌트에서 켜기 전에 사전에 활성화하는 데 Set-MsolDirSyncFeature cmdlet을 사용할 수 없습니다. 기능을 테스트하려면 새로운 Azure Active Directory 테넌트를 만들어야 합니다.

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>DirSyncProvisioningErrors로 개체 식별
중복 속성 충돌로 인해 이러한 오류가 있는 개체를 식별하는 방법은 현재 PowerShell Azure Active Directory 및 Office 365 관리자 포털로 두 가지 메서드가 있습니다. 향후 보고에 기반한 추가 포털로 확장할 계획이 있습니다.

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
이 항목에서 PowerShell cmdlet의 경우 다음은 true입니다.

* 다음 cmdlet은 모두 대/소문자 구분입니다.
* **–ErrorCategory PropertyConflict**는 항상 포함되어야 합니다. 현재 다른 종류의 **ErrorCategory**는 없지만 나중에 확장될 수 있습니다.

먼저 **Connect-MsolService**를 실행하고 테넌트 관리자에 대한 자격 증명을 입력하여 시작합니다.

그런 다음 다양한 방법으로 오류를 보려면 다음 cmdlet 및 연산자를 사용합니다.

1. [모두 표시](#see-all)
2. [속성 형식으로](#by-property-type)
3. [충돌 값으로](#by-conflicting-value)
4. [문자열 검색을 사용하여](#using-a-string-search)
5. [정렬](#sorted)
6. [제한된 수량 또는 모두](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>모두 표시
연결되면 테넌트에서 오류를 프로비저닝하는 특성의 일반 목록을 보기 위해 다음을 실행합니다.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

다음과 같은 결과가 생성됩니다.  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>속성 형식으로
속성 형식으로 오류를 보려면 **UserPrincipalName** 또는 **ProxyAddresses** 인수를 가진 **-PropertyName** 플래그를 추가합니다.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

또는

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>충돌 값으로
특정 속성에 관련된 오류를 확인하려면 **-PropertyValue** 플래그를 추가합니다(이 플래그를 추가하는 경우 **-PropertyName**을 함께 사용해야 함).

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>문자열 검색을 사용하여
광범위한 문자열 검색을 수행하려면 **-SearchString** 플래그를 사용합니다. 항상 필수 항목인 **-ErrorCategory PropertyConflict**를 제외하고 위의 모든 플래그와 독립적으로 사용할 수 있습니다.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>제한된 수량 또는 모두
1. **MaxResults<Int>**는 특정 값으로 쿼리를 제한하는 데 사용할 수 있습니다.
2. **All** 은 많은 오류가 있는 경우 검색되는 모든 결과를 확인하기 위해 사용할 수 있습니다.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Office 365 관리 포털
Office 365 관리 센터에서 디렉터리 동기화 오류를 볼 수 있습니다. Office 365 포털의 보고서는 이러한 오류가 있는 **사용자** 개체만을 표시합니다. **그룹** 및 **연락처** 간의 충돌에 대한 정보는 표시하지 않습니다.

![활성 사용자](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "활성 사용자")

Office 365 관리 센터에서 디렉터리 동기화 오류를 보는 방법에 대한 지침은 [Office 365에서 디렉터리 동기화 오류 확인](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)을 참조하세요.

### <a name="identity-synchronization-error-report"></a>ID 동기화 오류 보고서
중복 특성 충돌이 있는 개체가 이 새 동작으로 처리되는 경우 알림은 테넌트에 대한 기술 알림 문의로 전송되는 표준 ID 동기화 오류 보고서 전자 메일에 포함됩니다. 그러나 이 동작에서 중요한 변경 사항이 있습니다. 이전에 중복 특성 충돌에 대한 정보는 충돌이 해결될 때까지 모든 후속 오류 보고서에 포함됩니다. 이 새 동작으로 지정된 충돌에 대한 오류 알림이 충돌 특성이 격리되는 시간에 한 번만 나타납니다.

ProxyAddress 충돌에 대한 메일 알림의 예제는 다음과 같습니다.  
    ![활성 사용자](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "활성 사용자")  

## <a name="resolving-conflicts"></a>충돌 해결
이러한 오류에 대한 문제 해결 전략 및 해결 방법은 중복 특성 오류가 이전에 처리된 방식과 다르지 않습니다. 유일한 차이점은 타이머 작업은 서비스쪽 테넌트를 통해 스윕하여 충돌이 해결되면 적절한 개체에 문제의 특성을 자동으로 추가한다는 점입니다.

다음 문서에서는 다양한 문제 해결 및 해결 방법을 간략하게 설명합니다. [Office 365에서 중복되거나 잘못된 특성이 디렉터리 동기화를 방해할 경우](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>알려진 문제
이러한 알려진 문제로 인해 데이터 손실 또는 서비스 저하가 발생하지 않습니다. 그 중 일부는 심미적이며 다른 것은 충돌 특성을 격리하는 대신 표준 “*사전 복원력*” 중복 특성 오류를 throw하고 다른 것은 추가 수동 수정이 필요한 특정 오류를 발생시킵니다.

**핵심 동작:**

1. 특정 특성 구성이 있는 개체는 격리되는 중복 특성과 달리 내보내기 오류가 계속 수신됩니다.  
   예: 
   
    a. 새로운 사용자가 **Joe@contoso.com**의 UPN과 ProxyAddress **smtp:Joe@contoso.com**로 AD에서 만들어집니다.
   
    나. 이 개체의 속성이 ProxyAddress가 **SMTP:Joe@contoso.com**인 기존 그룹과 충돌합니다.
   
    다. 내보낼 때 충돌 특성이 격리되는 대신 **ProxyAddress 충돌** 오류가 발생합니다. 복구 기능이 활성화되기 전에 수행되므로 각 후속 동기화 주기 시 작업이 다시 시도됩니다.
2. 두 그룹이 동일한 SMTP 주소로 온-프레미스가 생성되는 경우 하나는 첫 번째 시도에서 표준 중복 **ProxyAddress** 오류로 프로비전하지 못합니다. 그러나 다음 동기화 주기 시 중복 값은 제대로 격리됩니다.

**Office 포털 보고서**:

1. UPN 충돌 집합에서 두 개체에 대한 자세한 오류 메시지는 같습니다. 이는 실제로 하나에만 변경된 데이터가 있는 경우 둘 모두 해당 UPN을 변경 / 격리했음을 나타냅니다.
2. UPN 충돌에 대한 자세한 오류 메시지는 해당 UPN을 변경하고 격리한 사용자에 대한 잘못된 displayName을 보여 줍니다. 예: 
   
    a. **사용자 A**는 먼저 **UPN = User@contoso.com**과 동기화합니다.
   
    나. **사용자 B**는 **UPN = User@contoso.com**과 동기화하려고 시도합니다.
   
    다. **사용자 B**의 UPN은 **User1234@contoso.onmicrosoft.com**로 변경되며 **User@contoso.com**이 **DirSyncProvisioningErrors**에 추가됩니다.
   
    d. **사용자 B**에 대한 오류 메시지는 **사용자 A**가 이미 **User@contoso.com**을 UPN으로 가지고 있음을 나타내야 하지만 **사용자 B** 고유의 displayName을 보여 줍니다.

**ID 동기화 오류 보고서**:

*이 문제를 해결하는 방법에 대한 단계*의 링크가 잘못되었습니다.  
    ![활성 사용자](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "활성 사용자")  

[https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency)를 가리켜야 합니다.

## <a name="see-also"></a>참고 항목
* [Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
* [Office 365에서 디렉터리 동기화 오류 확인](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)

