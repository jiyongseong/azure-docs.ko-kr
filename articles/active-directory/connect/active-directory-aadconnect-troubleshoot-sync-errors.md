---
title: 'Azure AD Connect: 동기화 중의 오류 문제 해결 | Microsoft Docs'
description: Azure AD Connect와의 동기화 중에 발생하는 오류의 문제 해결 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: aaa374d5a11ef5b5860f83a87386ff981319189f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="troubleshooting-errors-during-synchronization"></a>동기화 중 오류 문제 해결
ID 데이터가 Windows Server Active Directory(AD DS)로부터 Azure AD(Azure Active Directory)로 동기화되는 중에 오류가 발생할 수 있습니다. 이 문서에서는 여러 동기화 오류 유형, 오류가 발생할 수 있는 몇 가지 상황, 오류를 해결할 수 있는 가능한 방법에 대한 개요를 제공합니다. 이 문서는 일반적인 오류 유형을 다루며 가능한 모든 오류를 포괄하지 못할 수 있습니다.

 이 문서에서는 독자가 기본적인 [Azure AD 및 Azure AD Connect의 설계 개념](active-directory-aadconnect-design-concepts.md)에 대해 잘 알고 있다고 가정합니다

Azure AD Connect \(2016년 8월 이상\)의 최신 버전에서는 [Azure Portal](https://aka.ms/aadconnecthealth)에서 동기화에 대한 Azure AD Connect Health의 일환으로 동기화 오류 보고서를 제공합니다.

2016년 9월 1일부터 모든 *새* Azure Active Directory 테넌트에 대해 [Azure Active Directory Duplicate Attribute Resiliency](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) 기능이 기본적으로 활성화됩니다. 향후 몇 달 안에 기존 테넌트에 대해 이 기능이 자동으로 활성화될 것입니다.

Azure AD Connect는 가져오기, 동기화, 내보내기 등, 동기 상태를 유지하는 디렉터리로부터 3가지 유형의 작업을 수행합니다.  오류는 모든 작업에서 발생할 수 있습니다. 이 문서는 주로 Azure AD로 내보내는 중 발생하는 오류에 초점을 맞춥니다.

## <a name="errors-during-export-to-azure-ad"></a>Azure AD로 내보내는 중 오류
다음 섹션에서는 Azure AD 커넥터를 사용하여 Azure AD로의 내보내기 작업 중 발생할 수 있는 여러 동기화 오류 유형을 설명합니다. 이 커넥터는 "contoso.*onmicrosoft.com*" 이름 형식으로 식별할 수 있습니다.
Azure AD로 내보내는 중 오류는 Azure Active Directory에서 Azure AD Connect \(동기화 엔진\)이 시도한 \(추가, 업데이트, 삭제 등\)의 작업이 실패했음을 나타냅니다.

![내보내기 오류 개요](./media/active-directory-aadconnect-troubleshoot-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>데이터 불일치 오류
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>설명
* Azure AD Connect \(동기화 엔진\)이 Azure Active Directory에 개체 추가 또는 업데이트를 지시하면 Azure AD는 **sourceAnchor** 특성을 사용하여 들어오는 개체를 Azure AD 개체의 **immutableId** 특성과 일치시킵니다.  이 일치를 **하드 일치**라고 합니다.
* Azure AD가 **immutableId** 특성과 들어오는 개체의 **sourceAnchor** 특성이 일치하는 개체를 **찾지 못하면** 새 개체를 프로비전하기 전에 다시 ProxyAddresses 및 UserPrincipalName 특성을 사용하여 일치를 찾습니다. 이 일치를 **소프트 일치**라고 합니다. 소프트 일치는 Azure AD에 이미 있는 개체(Azure AD가 출처)를, 온-프레미스에서 동일한 엔터티(사용자, 그룹)를 나타내는 동기화 중에 추가/업데이트되는 새 개체와 일치시키도록 설계되었습니다.
* **InvalidSoftMatch** 오류는 하드 일치에서 일치하는 개체를 찾지 **못했고,** 소프트 매치에서는 일치하는 개체를 찾았으나 이 개체에 이미 수신 개체의 *SourceAnchor*와 다른 *immutableId* 값이 있어 이 일치하는 개체가 온-프레미스 Active Directory의 다른 개체와 동기화되었음을 나타내는 경우에 발생합니다.

즉 소프트 일치가 작동하려면 소프트 일치 개체에 *immutableId*에 대한 값이 없어야 합니다. 값이 설정된 *immutableId* 개체가 하드 일치에는 실패하고 소프트 일치 기준은 만족할 경우 이 작업에서는 InvalidSoftMatch 동기화 오류가 발생합니다.

Azure Active Directory 스키마에서는 다음 특성의 값이 둘 이상의 개체에서 동일할 수 없습니다. \(전체 목록은 아닙니다.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Azure AD Attribute Duplicate Attribute Resiliency](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) 기능도 Azure Active Directory의 기본 동작으로 롤아웃될 수 있습니다.  이렇게 하면 Azure AD가 온-프레미스 AD 환경에 있는 중복된 ProxyAddresses 및 UserPrincipalName 특성을 처리하는 방식에서 복원력이 향상되어 Azure AD Connect(및 다른 동기화 클라이언트)에서 발생하는 동기화 오류의 수가 줄어듭니다. 이 기능은 중복 오류를 수정하지 않습니다. 따라서 여전히 데이터 수정이 필요합니다. 하지만 Azure AD의 중복 값으로 인해 다른 방식으로는 프로비전될 수 없는 새로운 개체의 프로비전이 가능해집니다. 또한 동기화 클라이언트로 반환되는 동기화 오류 수가 줄어듭니다.
> 테넌트에 이 기능을 사용하는 경우 새 개체의 프로비전 과정에서 InvalidSoftMatch 동기화 오류가 표시되지 않습니다.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>InvalidSoftMatch의 예제 시나리오
1. 온-프레미스 Active Directory에 동일한 ProxyAddresses 특성 값을 갖는 개체가 둘 이상 존재합니다. Azure AD에서는 하나만 프로비전되고 있습니다.
2. 온-프레미스 Active Directory에 동일한 userPrincipalName 값을 갖는 개체가 둘 이상 존재합니다. Azure AD에서는 하나만 프로비전되고 있습니다.
3. 온-프레미스 Active Directory에 Azure Active Directory의 기존 개체와 ProxyAddresses 특성 값이 같은 개체가 추가되었습니다. 온-프레미스에 추가된 개체는 Azure Active Directory에서 프로비전되지 않습니다.
4. 온-프레미스 Active Directory에 Azure Active Directory의 계정과 userPrincipalName 특성 값이 같은 개체가 추가되었습니다. 이 개체는 Azure Active Directory에서 프로비전되지 않습니다.
5. 동기화된 계정이 포리스트 A에서 포리스트 B로 옮겨졌습니다. Azure AD Connect(동기화 엔진)는 ObjectGUID 특성을 사용하여 SourceAnchor를 계산했습니다. 포리스트 이동 후 SourceAnchor 값이 다릅니다. 새 개체(포리스트 B)가 Azure AD의 기존 개체와 동기화하지 못합니다.
6. 동기화된 개체가 우발적으로 온-프레미스 Active Directory에서 삭제되고 동일한 개체에 대해 Azure Active Directory의 계정 삭제 없이 새 개체가 Active Directory에 생성되었습니다(예: 사용자). 새 계정이 기존 Azure AD 개체와 동기화되지 않습니다.
7. Azure AD Connect를 제거 후 다시 설치했습니다. 다시 설치하는 동안 다른 특성을 SourceAnchor로 선택했습니다. 이전에 동기화된 모든 개체의 동기화가 중단되고 InvalidSoftMatch 오류가 발생합니다.

#### <a name="example-case"></a>예제 사례:
1. **Bob Smith**는 *contoso.com*의 온-프레미스 Active Directory로부터 Azure Active Directory의 사용자와 동기화됩니다.
2. Bob Smith의 **UserPrincipalName**은어 **bobs@contoso.com**으로 설정되었습니다.
3. **"abcdefghijklmnopqrstuv=="**은 Azure AD Connect가 온-프레미스 Active Directory에서 Bob Smith의 **objectGUID**를 사용하여 산출한 **SourceAnchor**로, Azure Active Directory에서 Bob Smith에 대한 **immutableId**입니다.
4. 또한 **proxyAddresses** 특성에는 다음 값이 있습니다.
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
5. 새 사용자 **Bob Taylor**가 온-프레미스 Active Directory에 추가됩니다.
6. Bob Taylor의 **UserPrincipalName**은 **bobt@contoso.com**으로 설정됩니다.
7. **"abcdefghijkl0123456789==""**은 온-프레미스 Active Directory에서 Bob Taylor의 **objectGUID**를 사용하여 Azure AD Connect가 산출한**sourceAnchor**입니다. Bob Taylor의 개체는 아직 Azure Active Directory에 동기화되지 않았습니다.
8. Bob Taylor의 proxyAddresses 특성 값은 다음과 같습니다.
   * smtp:bobt@contoso.com
   * smtp:bob.taylor@contoso.com
   * **smtp:bob@contoso.com**
9. 동기화하는 동안 Azure AD Connect는 온-프레미스 Active Directory에서 Bob Taylor의 추가를 인식하고 Azure AD에 동일한 변경을 요청합니다.
10. Azure AD는 먼저 하드 일치를 수행합니다. 즉 immutableId가 "abcdefghijkl0123456789=="인 개체가 있는지 검색합니다. Azure AD에 이 immutableId를 갖는 다른 개체가 없으므로 하드 일치가 실패합니다.
11. 다음으로 Azure AD는 Bob Taylor 일치를 시도합니다. 즉 smtp:bob@contoso.com을 포함한 세 값과 일치하는 proxyAddresses를 갖는 개체가 있는지 검색합니다.
12. Azure AD는 소프트 일치 조건에 맞는 Bob Smith의 개체를 찾습니다. 그러나 이 개체는 immutableId = "abcdefghijklmnopqrstuv==" 값을 갖습니다. 즉 이 개체가 온-프레미스 Active Directory의 다른 개체와 동기화되었음을 나타냅니다. 따라서 Azure AD는 이 개체에 소프트 일치를 수행할 수 없고 **InvalidSoftMatch** 동기화 오류가 발생합니다.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>InvalidSoftMatch 오류 해결 방법
InvalidSoftMatch 오류가 발생하는 가장 일반적인 원인은 SourceAnchor \(immutableId\)가 다른 두 개체가 Azure AD에서 소프트 일치 프로세스 중에 사용되는 ProxyAddresses 및/또는 UserPrincipalName 특성에 동일한 값을 갖기 때문입니다. 잘못된 소프트 일치 해결 방법

1. 중복된 proxyAddresses, userPrincipalName 또는 기타 오류의 원인이 된 특성 값을 파악합니다. 또한 충돌에 연루된 둘 \(이상의\) 개체를 식별합니다. [동기화에 대한 Azure AD Connect Health](https://aka.ms/aadchsyncerrors)가 생성한 보고서가 이 두 개체를 찾는 데 도움이 될 수 있습니다.
2. 어떤 개체가 중복 값을 그대로 유지하고 어떤 개체가 그렇지 않은지 파악합니다.
3. 그 값을 가져서는 안 되는 개체에서 중복 값을 제거합니다. 개체의 출처가 되는 디렉터리에서 변경을 수행해야 합니다. 경우에 따라 충돌 개체 중 하나를 삭제해야 할 수 있습니다.
4. 온-프레미스 AD에서 변경할 경우 Azure AD Connect가 변경을 동기화하게 합니다.

동기화를 위한 Azure AD Connect Health 내 동기화 오류 보고서는 30분 간격으로 업데이트되며 최신 동기화 시도에서의 오류를 포함합니다.

> [!NOTE]
> ImmutableId는 기본적으로 개체의 수명 주기 동안 변경되지 않아야 합니다. Azure AD Connect가 위 목록의 시나리오와 상당 부분 다르게 구성된 경우, 결국 계속 사용하려는 기존 Azure AD 개체가 있는 동일한 엔터티(동일한 사용자/그룹/연락처 등)를 나타내는 AD 개체에 대해 Azure AD Connect가 다른 SourceAnchor 값을 계산하는 상황일 수 있습니다. 
>
>

#### <a name="related-articles"></a>관련 문서
* [Office 365에서 디렉터리 동기화를 방해하는 중복 또는 잘못된 특성](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>설명
Azure AD가 두 개체의 소프트 일치를 시도할 때 "개체 유형"(예: 사용자, 그룹, 연락처 등)이 다른 두 개체가 소프트 일치 수행에 사용된 특성에 대해 동일한 값을 가질 수 있습니다. 이러한 특성의 중복은 Azure AD에서 허용되지 않으므로 이 작업에서 "ObjectTypeMismatch" 동기화 오류가 발생합니다.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>ObjectTypeMismatch 오류의 예제 시나리오
* Office 365에서 메일을 지원하는 보안 그룹이 만들어집니다. 관리자가 온-프레미스 AD(아직 Azure AD와 동기화되지 않음)에 Office 365 그룹과 ProxyAddresses 특성 값이 같은 새 사용자나 연락처를 추가합니다.

#### <a name="example-case"></a>예제 사례
1. 관리자가 Office 365에서 Tax 부서에 대해 새 메일 지원 보안 그룹을 만들고 이메일 주소를 tax@contoso.com으로 제공합니다. **smtp:tax@contoso.com**의 값으로 이 그룹에 대한 ProxyAddresses 특성을 할당합니다.
2. 새 사용자가 Contoso.com에 가입하고 이 사용자에 대해 온-프레미스에서 proxyAddress가 **smtp:tax@contoso.com**인 계정이 만들어집니다. 
3. Azure AD Connect가 새 사용자 계정을 동기화하면 "ObjectTypeMismatch" 오류가 발생합니다.

#### <a name="how-to-fix-objecttypemismatch-error"></a>ObjectTypeMismatch 오류 해결 방법
ObjectTypeMismatch 오류의 가장 일반적인 원인은 두 개체의 다른 유형(사용자, 그룹, 연락처 등)이 동일한 ProxyAddresses 특성 값을 갖기 때문입니다. ObjectTypeMismatch를 수정하려면:

1. 오류의 원인이 된 중복된 proxyAddresses(또는 기타 특성) 값을 파악합니다. 또한 충돌에 연루된 둘 \(이상의\) 개체를 식별합니다. [동기화에 대한 Azure AD Connect Health](https://aka.ms/aadchsyncerrors)가 생성한 보고서가 이 두 개체를 찾는 데 도움이 될 수 있습니다.
2. 어떤 개체가 중복 값을 그대로 유지하고 어떤 개체가 그렇지 않은지 파악합니다.
3. 그 값을 가져서는 안 되는 개체에서 중복 값을 제거합니다. 개체의 출처가 되는 디렉터리에서 변경을 수행해야 합니다. 경우에 따라 충돌 개체 중 하나를 삭제해야 할 수 있습니다.
4. 온-프레미스 AD에서 변경할 경우 Azure AD Connect가 변경을 동기화하게 합니다. 동기화를 위한 Azure AD Connect Health 내 동기화 오류 보고서는 30분 간격으로 업데이트되며 최신 동기화 시도에서의 오류를 포함합니다.

## <a name="duplicate-attributes"></a>중복 특성
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>설명
Azure Active Directory 스키마에서는 다음 특성의 값이 둘 이상의 개체에서 동일할 수 없습니다. Azure AD의 각 개체는 특정 인스턴스에서 다음 특성에 대해 고유한 값을 가져야 합니다.

* ProxyAddresses
* UserPrincipalName

Azure AD Connect가 이미 Azure Active Directory의 다른 개체에 할당된 위의 특성 값에 대해 새 개체를 추가하거나 기존 개체를 업데이트하려 하면 작업에서 "AttributeValueMustBeUnique" 동기화 오류가 발생합니다.

#### <a name="possible-scenarios"></a>가능한 시나리오:
1. 중복 값이 이미 동기화된 개체에 할당되어 다른 동기화 개체와 충돌합니다.

#### <a name="example-case"></a>예제 사례:
1. **Bob Smith**는 contoso.com의 온-프레미스 Active Directory로부터 Azure Active Directory의 사용자와 동기화됩니다.
2. Bob Smith의 온-프레미스 **UserPrincipalName**이 **bobs@contoso.com**으로 설정됩니다.
3. 또한 **proxyAddresses** 특성에는 다음 값이 있습니다.
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
4. 새 사용자 **Bob Taylor**가 온-프레미스 Active Directory에 추가됩니다.
5. Bob Taylor의 **UserPrincipalName**은 **bobt@contoso.com**으로 설정됩니다.
6. **Bob Taylor**의 **ProxyAddresses** 특성 값은 i. smtp:bobt@contoso.com ii입니다. smtp:bob.taylor@contoso.com
7. Bob Taylor의 개체가 Azure AD에 성공적으로 동기화됩니다.
8. 관리자가 Bob Taylor의 **ProxyAddresses** 특성을 i 값으로 업데이트하기로 결정했습니다. **smtp:bob@contoso.com**
9. Azure AD가 Azure AD의 Bob Taylor 개체를 위의 값으로 업데이트하려 하지만 이미 ProxyAddresses 값이 Bob Smith에게 할당되었으므로 이 작업이 실패하고 "AttributeValueMustBeUnique" 오류가 발생합니다.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>AttributeValueMustBeUnique 오류 해결 방법
AttributeValueMustBeUnique 오류가 발생하는 가장 일반적인 원인은 SourceAnchor \(immutableId\)가 다른 두 개체가 ProxyAddresses 및/또는 UserPrincipalName 특성에 동일한 값을 갖기 때문입니다. AttributeValueMustBeUnique 오류 해결 방법

1. 중복된 proxyAddresses, userPrincipalName 또는 기타 오류의 원인이 된 특성 값을 파악합니다. 또한 충돌에 연루된 둘 \(이상의\) 개체를 식별합니다. [동기화에 대한 Azure AD Connect Health](https://aka.ms/aadchsyncerrors)가 생성한 보고서가 이 두 개체를 찾는 데 도움이 될 수 있습니다.
2. 어떤 개체가 중복 값을 그대로 유지하고 어떤 개체가 그렇지 않은지 파악합니다.
3. 그 값을 가져서는 안 되는 개체에서 중복 값을 제거합니다. 개체의 출처가 되는 디렉터리에서 변경을 수행해야 합니다. 경우에 따라 충돌 개체 중 하나를 삭제해야 할 수 있습니다.
4. 온-프레미스 AD에서 변경할 경우 Azure AD Connect가 오류 수정을 위해 변경을 동기화하게 합니다.

#### <a name="related-articles"></a>관련 문서
-[Office 365에서 디렉터리 동기화를 방해하는 중복 또는 잘못된 특성](https://support.microsoft.com/en-us/kb/2647098)

## <a name="data-validation-failures"></a>데이터 유효성 검사 실패
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>설명
Azure Active Directory는 데이터를 디렉터리에 쓰도록 허용하기 전에 데이터 자체에 다양한 제약을 적용합니다. 이것은 응용 프로그램을 사용하는 동안 최종 사용자에게 가능한 최적의 환경을 제공하기 위한 것으로, 이 데이터가 환경을 좌우합니다.

#### <a name="scenarios"></a>시나리오
a. UserPrincipalName 특성 값에 잘못된/지원되지 않는 문자가 있습니다.
나. UserPrincipalName 특성이 필요한 형식을 따르지 않습니다.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>IdentityDataValidationFailed 오류 해결 방법
a. UserPrincipalName 특성이 지원되는 문자와 필요한 형식을 따르는지 확인합니다.

#### <a name="related-articles"></a>관련 문서
* [Office 365 디렉터리 동기화를 통한 사용자 프로비전 준비](https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>설명
사용자의 UserPrincipalName 접미사가 한 페더레이션된 도메인에서 다른 페더레이션된 도메인으로 변경되었을 때 **“FederatedDomainChangeError”** 동기화 오류를 초래하는 특정한 경우입니다.

#### <a name="scenarios"></a>시나리오
동기화된 사용자의 경우 UserPrincipalName 접미사가 하나의 페더레이션된 도메인에서 온-프레미스의 다른 페더레이션된 도메인으로 변경되었습니다. 예를 들어, *UserPrincipalName = bob@contoso.com*가 *UserPrincipalName = bob@fabrikam.com*으로 변경되었습니다.

#### <a name="example"></a>예
1. Contoso.com 계정인 Bob Smith가 Active Directory에서 UserPrincipalName bob@contoso.com으로 새 사용자로 추가되었습니다.
2. Bob이 Contoso.com에서 Fabrikam.com이라는 다른 부서로 옮겨졌고 UserPrincipalName이 bob@fabrikam.com으로 변경되었습니다.
3. Contoso.com과 fabrikam.com 도메인은 모두 Azure Active Directory와 페더레이션된 도메인입니다.
4. Bob의 userPrincipalName이 업데이트되지 않아 “FederatedDomainChangeError” 동기화 오류가 발생합니다.

#### <a name="how-to-fix"></a>해결 방법
사용자의 UserPrincipalName 접미사가 bob@**contoso.com**에서 bob@**fabrikam.com**으로 업데이트되었고 **contoso.com**과 **fabrikam.com**이 모두 **페더레이션된 도메인**이면 이러한 단계에 따라 동기화 오류를 수정합니다.

1. Azure AD에 있는 사용자의 UserPrincipalName을 bob@contoso.com에서 bob@contoso.onmicrosoft.com으로 업데이트합니다. 다음 PowerShell 명령을 Azure AD PowerShell 모듈과 함께 사용할 수 있습니다. `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. 다음 동기화 주기에서 동기화 시도를 허용합니다. 이번에는 동기화에 성공하고 Bob의 UserPrincipalName이 예상대로 bob@fabrikam.com으로 업데이트됩니다.

#### <a name="related-articles"></a>관련 문서
* [다른 페더레이션된 도메인을 사용하기 위해 사용자 계정의 UPN을 변경한 후에 Azure Active Directory 동기화 도구에서 변경 사항이 동기화되지 않습니다(영문).](https://support.microsoft.com/en-us/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>설명
한 특성이 Azure Active Directory 스키마에서 정한 허용된 크기 제한, 길이 제한 또는 수 제한을 초과할 경우 동기화 작업에서 **LargeObject** 또는 **ExceededAllowedLength** 동기화 오류가 발생합니다. 일반적으로 이 오류는 다음 특성에 대해 발생합니다.

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>가능한 시나리오
1. Bob의 userCertificate 특성이 Bob에게 할당된 너무 많은 인증서를 저장하고 있습니다. 여기에는 오래되어 만료된 인증서가 포함될 수 있습니다. 하드 한도는 15개의 인증서입니다. userCertificate 특성에서 LargeObject 오류를 처리하는 방법에 대한 자세한 내용은 [userCertificate 특성으로 인한 LargeObject 오류 처리](active-directory-aadconnectsync-largeobjecterror-usercertificate.md) 문서를 참조하세요.
2. Bob의 userSMIMECertificate 특성이 Bob에게 할당된 너무 많은 인증서를 저장하고 있습니다. 여기에는 오래되어 만료된 인증서가 포함될 수 있습니다. 하드 한도는 15개의 인증서입니다.
3. Active Directory에서 설정한 Bob의 thmubnailPhoto가 너무 커 Azure AD에서 동기화할 수 없습니다.
4. Active Directory의 ProxyAddresses 특성 자동 입력 중에 개체에 너무 많은 ProxyAddresses가 할당되었습니다.

### <a name="how-to-fix"></a>해결 방법
1. 오류를 초래하는 특성이 허용 범위 안에 있는지 확인합니다.

## <a name="related-links"></a>관련 링크
* [Active Directory 관리 센터에서 Active Directory 개체 찾기](https://technet.microsoft.com/library/dd560661.aspx)
* [Azure Active Directory PowerShell을 사용하여 개체에 대해 Azure Active Directory를 쿼리하는 방법](https://msdn.microsoft.com/library/azure/jj151815.aspx)
