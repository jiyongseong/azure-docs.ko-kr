---
title: 'Azure AD Connect 동기화: 기본 구성 이해 | Microsoft Docs'
description: 이 문서에서는 Azure AD Connect 동기화의 기본 구성을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: dba7a6fcf936e9610a5f1f04e367d32e9aae6643
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/08/2018
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect 동기화: 기본 구성 이해
이 문서는 기본 구성 규칙을 설명합니다. 규칙 및 해당 규칙이 구성에 어떤 영향을 주는지를 문서화합니다. 또한 Azure AD Connect 동기화의 기본 구성을 안내합니다. 목표는 판독기로 선언적 프로비전이라고 명명된 구성 모델이 실제 예제에서 작동하는 방식을 이해하는 것입니다. 이 문서에서는 설치 마법사를 사용하여 Azure AD Connect 동기화를 설치한 뒤 구성하는 상황을 가정합니다.

구성 모델의 세부 정보를 이해하려면 [선언적 프로비전 이해](active-directory-aadconnectsync-understanding-declarative-provisioning.md)를 읽어 보세요.

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>온-프레미스에서 Azure AD로 기본 규칙
다음 식은 기본 구성에서 찾을 수 있습니다.

### <a name="user-out-of-box-rules"></a>사용자 기본 규칙
또한 이러한 규칙은 iNetOrgPerson 개체 형식에 적용됩니다.

사용자 개체를 동기화하려면 다음을 만족해야 합니다.

* sourceAnchor가 있어야 합니다.
* Azure AD에서 개체를 만든 후에 sourceAnchor는 변경할 수 없습니다. 값이 변경된 온-프레미스인 경우 sourceAnchor가 이전 값으로 다시 변경될 때까지 개체의 동기화는 중지됩니다.
* accountEnabled(userAccountControl) 특성이 채워져야 합니다. 온-프레미스 Active Directory를 통해 이 특성은 항상 존재하고 채워집니다.

다음 사용자 개체는 Azure AD에 동기화되지 **않습니다** .

* `IsPresent([isCriticalSystemObject])`. 기본 제공 관리자 계정과 같은 Active Directory의 많은 기본 개체가 동기화되지 않도록 합니다.
* `IsPresent([sAMAccountName]) = False`. sAMAccountName 특성이 없는 사용자 개체가 동기화되지 않도록 합니다. 이 경우는 실질적으로 NT4에서 업그레이드된 도메인에 발생합니다.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Azure AD Connect 동기화 및 이전 버전에서 사용한 서비스 계정을 동기화하지 않습니다.
* Exchange Online에서 작동하지 않는 Exchange 계정을 동기화하지 않습니다.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Exchange Online에서 작동하지 않는 개체를 동기화하지 않습니다.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  이 비트 마스크(&amp;H21C07000)는 다음 개체도 필터링합니다.
  * 메일 사용이 가능한 공용 폴더(미리 보기 버전 1.1.524.0부터)
  * 시스템 도우미 사서함
  * 사서함 데이터베이스 사서함(시스템 사서함)
  * 유니버설 보안 그룹(사용자에 대해 적용하지 않지만 레거시를 지원하기 위해 존재합니다)
  * 비유니버설 그룹(사용자에 대해 적용하지 않지만 레거시를 지원하기 위해 존재합니다)
  * 사서함 계획
  * 검색 사서함
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)` 복제 피해 개체를 동기화하지 않습니다.

다음 특성 규칙이 적용됩니다.

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. sourceAnchor 특성은 연결된 사서함에서 제공되지 않습니다. 실제 계정에서 연결된 사서함을 찾은 경우 실제 계정이 나중에 조인된다고 가정합니다.
* Exchange 관련 특성은 특성 **mailNickName** 에 값이 있는 경우 동기화됩니다.
* 포리스트가 여러 개인 경우 특성은 다음 순서대로 사용됩니다.
  1. 로그온에 관련된 특성(예: userPrincipalName)은 활성화된 계정을 사용하여 포리스트에서 제공됩니다.
  2. Exchange GAL(전체 주소 목록)에서 찾을 수 있는 특성은 Exchange 사서함이 있는 포리스트에서 제공됩니다.
  3. 사서함이 없는 경우 이러한 특성은 포리스트에서 가져올 수 있습니다.
  4. Exchange 관련 특성(GAL에 표시되지 않는 기술 특성)은 `mailNickname ISNOTNULL`의 포리스트에서 제공됩니다.
  5. 이러한 규칙 중 하나를 만족하는 다중 포리스트가 있는 경우 어떤 포리스트가 특성을 제공할지 결정하는 데 커넥터(포리스트)의 생성 순서(날짜/시간)가 사용됩니다.

### <a name="contact-out-of-box-rules"></a>연락처 기본 규칙
연락처 개체를 동기화하려면 다음을 만족해야 합니다.

* 연락처는 메일을 사용할 수 있어야 합니다. 다음 규칙을 통해 확인합니다.
  * `IsPresent([proxyAddresses]) = True)`. proxyAddresses 특성을 채워야 합니다.
  * 기본 전자 메일 주소는 proxyAddresses 특성 또는 메일 특성에서 찾을 수 있습니다. @의 유무는 콘텐츠가 전자 메일 주소인지 확인하는 데 사용됩니다. 이 두 가지 규칙 중 하나가 True로 평가되어야 합니다.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))` "SMTP:"가 있는 항목이 있나요? 있다면 문자열에서 @를 찾을 수 있나요?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)` 메일 특성이 채워지나요? 그리고 채워진다면 문자열에서 @를 찾을 수 있나요?

다음 연락처 개체는 Azure AD에 동기화되지 **않습니다** .

* `IsPresent([isCriticalSystemObject])` 중요로 표시된 연락처 개체가 동기화되지 않도록 합니다. 기본 구성을 사용하지 않아야 합니다.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. 해당 개체는 Exchange Online에서 작동하지 않습니다.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. 복제 피해 개체를 동기화하지 않습니다.

### <a name="group-out-of-box-rules"></a>그룹 기본 규칙
그룹 개체를 동기화하려면 다음을 만족해야 합니다.

* 50,000명 미만의 멤버가 있어야 합니다. 이 개수는 온-프레미스 그룹의 멤버 수입니다.
  * 동기화를 처음으로 시작하기 전에 더 많은 멤버가 있으면 그룹은 동기화되지 않습니다.
  * 처음 만들어질 때 멤버 수가 증가하면 50,000명의 멤버에 도달할 때 멤버 자격 수가 다시 50,000보다 낮아질 때까지 동기화가 중지됩니다.
  * 참고: 또한 50,000명의 멤버 자격 수는 Azure AD에서 적용됩니다. 이 규칙을 수정하거나 제거하는 경우 더 많은 멤버가 있는 그룹을 동기화할 수 없습니다.
* 그룹이 **배포 그룹**인 경우 메일을 사용할 수 있어야 합니다. 이러한 규칙이 적용되는 데 대해 [연락처 기본 규칙](#contact-out-of-box-rules)을 참조하세요.

다음 그룹 개체는 Azure AD에 동기화되지 **않습니다** .

* `IsPresent([isCriticalSystemObject])` 기본 제공 관리자 그룹과 같은 Active Directory의 많은 기본 개체가 동기화되지 않도록 합니다.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. DirSync에서 사용하는 레거시 그룹입니다.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. 역할 그룹입니다.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)` 복제 피해 개체를 동기화하지 않습니다.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal 기본 규칙
FSP는 메타버스에서 "어떤"(\*) 개체에 조인됩니다. 실제로 이 조인은 사용자 및 보안 그룹의 경우에만 발생합니다. 이 구성을 통해 포리스트 간 멤버 자격을 확인하고 Azure AD에 올바르게 표시합니다.

### <a name="computer-out-of-box-rules"></a>컴퓨터 기본 규칙
컴퓨터 개체를 동기화하려면 다음을 만족해야 합니다.

* `userCertificate ISNOTNULL`. Windows 10 컴퓨터만이 이 특성을 채웁니다. 이 특성의 값을 가진 컴퓨터 개체를 모두 동기화합니다.

## <a name="understanding-the-out-of-box-rules-scenario"></a>기본 규칙 시나리오 이해
이 예제에서 계정 포리스트(A), 리소스 포리스트(R) 및 Azure AD 디렉터리가 각각 하나씩 포함된 배포를 사용합니다.

![시나리오 설명이 있는 그림](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

이 구성에서 계정 포리스트의 활성화된 계정 및 연결된 사서함이 있는 리소스 포리스트의 비활성화된 계정이 있다고 가정합니다.

기본 구성에서 목표는 다음과 같습니다.

* 로그인에 관련된 특성은 설정된 계정이 있는 포리스트에서 동기화됩니다.
* GAL(전체 주소 목록)에서 찾을 수 있는 특성은 사서함이 있는 포리스트에서 동기화됩니다. 사서함을 찾을 수 없는 경우 다른 포리스트가 사용됩니다.
* 연결된 사서함이 있는 경우 Azure AD로 내보낼 개체에 대해 연결되고 활성화된 계정을 찾을 수 있어야 합니다.

### <a name="synchronization-rule-editor"></a>동기화 규칙 편집기
구성은 SRE(동기화 규칙 편집기) 도구를 사용하여 확인하고 변경하며 해당 바로 가기는 시작 메뉴에서 찾을 수 있습니다.

![동기화 규칙 편집기 아이콘](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

SRE는 리소스 키트 도구이며 Azure AD Connect 동기화와 함께 설치됩니다. 시작할 수 있으려면 사용자가 ADSyncAdmins 그룹의 멤버여야 합니다. 시작할 때 다음과 같이 표시됩니다.

![동기화 규칙 인바운드](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

이 창에서 사용자의 구성을 위해 만든 모든 동기화 규칙을 보게 됩니다. 테이블의 각 줄은 하나의 동기화 규칙입니다. 왼쪽의 규칙 우형 아래에 인바운드 및 아웃바운드의 두 가지 유형이 나열됩니다. 인바운드 및 아웃바운드는 메타버스의 보기에서 제공됩니다. 이 개요에서는 주로 인바운드 규칙에 집중합니다. 동기화 규칙의 실제 목록은 AD에서 검색된 스키마에 따라 달라집니다. 위의 그림에서 계정 포리스트(fabrikamonline.com)에는 Exchange, Lync 등의 서비스가 없으며 이러한 서비스에 대해 어떤 동기화 규칙도 만들어지지 않았습니다. 그러나 리소스 포리스트(res.fabrikamonline.com)에서 이러한 서비스에 대한 동기화 규칙을 찾을 수 있습니다. 규칙의 콘텐츠는 검색된 버전에 따라 다릅니다. 예를 들어 Exchange 2013를 사용한 배포에서는 Exchange 2010/2007에서 보다 자세한 특성 흐름이 구성되었습니다.

### <a name="synchronization-rule"></a>동기화 규칙
동기화 규칙은 조건을 만족할 때 흐르는 특성 집합이 포함된 구성 개체입니다. 또한 커넥터 공간에는 개체가 **조인** 또는 **일치**로 알려진 메타버스에 있는 개체와 관련되는 방법을 설명하기 위해서도 사용됩니다. 동기화 규칙에는 서로 관계를 나타내는 우선 순위 값이 있습니다. 낮은 숫자 값을 가진 동기화 규칙이 더 높은 우선 순위를 가지며 특성 흐름 충돌에서 높은 우선 순위가 충돌을 해결합니다.

한 예로 동기화 규칙 **AD에서 들어오기 – 사용자 AccountEnabled**를 살펴보겠습니다. SRE에서 이 줄을 표시하고 **편집**을 선택합니다.

이 규칙이 기본 규칙이므로 규칙을 열 때 경고를 받게 됩니다. [기본 규칙을 변경](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)하지 않아야 하므로 사용자의 의도를 묻습니다. 이 경우 단순히 규칙을 확인하려고 합니다. **아니오**를 선택합니다.

![동기화 규칙 경고](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

동기화 규칙에는 설명, 범위 지정 필터, 조인 규칙 및 변환의 네 가지 구성 섹션이 있습니다.

#### <a name="description"></a>설명
첫 번째 섹션은 이름 및 설명과 같은 기본 정보를 제공합니다.

![동기화 규칙 편집기의 설명 탭 ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

이 규칙이 관련된 연결된 시스템, 적용되는 연결된 시스템의 개체 유형 및 메타버스 개체 유형에 관한 정보를 찾을 수도 있습니다. 메타버스 개체 형식은 소스 개체 형식이 사용자, iNetOrgPerson 또는 연락처인지와 관계 없이 항상 사람입니다. 메타버스 개체 형식은 일반 형식으로 생성되어야 하므로 변경되어서는 안됩니다. 링크 형식은 조인, StickyJoin, 또는 프로 비전으로 설정할 수 있습니다. 이 설정은 조인 규칙 섹션과 함께 작동하며 나중에 설명됩니다.

또한 이 동기화 규칙이 암호 동기화에 사용됨을 확인할 수 있습니다. 사용자가 이 동기화 규칙에 대한 범위에 있는 경우 암호는 온-프레미스에서 클라우드로 동기화됩니다(암호 동기화 기능을 사용한다고 가정).

#### <a name="scoping-filter"></a>범위 지정 필터
범위 지정 필터 섹션은 동기화 규칙을 적용할 시기를 구성하는 데 사용됩니다. 검색하려는 동기화 규칙의 이름을 보면 활성화된 사용자에게만 적용해야 함을 알 수 있으므로 AD 특성 **userAccountControl** 에는 비트 2가 설정되지 않도록 범위가 구성됩니다. 동기화 엔진이 AD에서 사용자를 찾을 때 **userAccountControl** 이 10진수 값 512(활성화된 일반 사용자)로 설정된 경우 이 동기화 규칙을 적용합니다. 사용자가 **userAccountControl**을 514(비활성화된 일반 사용자)로 설정한 경우 규칙이 적용되지 않습니다.

![동기화 규칙 편집기의 범위 지정 탭 ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

범위 지정 필터에는 중첩될 수 있는 그룹 및 절이 있습니다. 동기화 규칙을 적용하려면 그룹 내의 모든 절을 충족해야 합니다. 여러 그룹이 정의된 경우, 하나 이상의 그룹이 적용할 규칙에 충족되어야 합니다. 즉, 논리 OR은 그룹들 간에 평가되며 논리 AND는 하나의 그룹 내에서 평가됩니다. 이 구성의 예는 **AAD로 나가기 - 그룹 조인**아웃바운드 동기화 규칙에서 찾을 수 있습니다. 예를 들어 몇 가지 동기화 필터 그룹, 보안 그룹에 대한 하나의 필터 그룹(`securityEnabled EQUAL True`) 및 배포 그룹에 대한 하나의 필터 그룹(`securityEnabled EQUAL False`)이 있습니다.

![동기화 규칙 편집기의 범위 지정 탭 ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

이 규칙은 Azure AD에 프로비전해야 하는 그룹을 정의하는 데 사용됩니다. 배포 그룹은 Azure AD와 동기화할 수 있도록 메일을 설정해야 하지만 보안 그룹에 대해서는 필요하지 않습니다.

#### <a name="join-rules"></a>조인 규칙
세 번째 섹션은 커넥터 공간의 개체가 메타버스의 개체와 관련되는 방법을 구성하는데 사용됩니다. 이전에 살펴본 규칙에는 조인 규칙에 대한 구성은 없으며, 대신 **AD에서 들어오기 – 사용자 조인**에서 살펴봅니다.

![동기화 규칙 편집기의 조인 규칙 탭 ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

조인 규칙의 콘텐츠는 설치 마법사에서 선택한 일치 옵션에 따라 달라집니다. 인바운드 규칙의 경우, 평가는 원본 커넥터 공간에서 개체와 함께 시작되며 조인 규칙의 각 그룹이 순서대로 평가됩니다. 원본 개체가 조인 규칙 중 하나를 사용하는 메타버스로의 개체 하나와 정확히 일치하는 것으로 평가되는 경우, 개체는 조인됩니다. 모든 규칙이 평가되며 일치 항목이 없는 경우, 설명 페이지의 링크 형식이 사용됩니다. 이 구성이 **프로비전**에 설정된 경우 대상, 메타버스에서 새 개체가 만들어집니다. 메타버스에 새 개체를 프로비전하는 것은 개체를 메타버스에 **프로젝트** 하는 것이라고도 합니다.

조인 규칙은 한 번만 평가됩니다. 커넥터 공간 개체 및 메타버스 개체가 결합된 경우, 동기화 규칙의 범위가 충족되는 한 조인 상태를 유지합니다.

동기화 규칙을 평가할 때 정의된 조인 규칙이 포함된 동기화 규칙 하나만이 범위 내에 있어야 합니다. 조인 규칙이 포함된 여러 동기화 규칙이 하나의 개체에 대해 발견되면 오류가 발생합니다. 이러한 이유로, 여러 동기화 규칙이 개체의 범위에 있을 경우 정의된 조인을 포함한 하나의 동기화 규칙만이 있는 것이 가장 좋습니다. Azure AD Connect 동기화에 대한 기본 구성에서 이름으로 이 규칙을 찾을 수 있으며 이름의 끝에서 **조인** 이라는 단어가 있는 규칙을 찾습니다. 다른 동기화 규칙이 개체와 함께 결합되거나 대상의 새 개체를 프로비전하는 경우 정의된 조인 규칙이 없는 동기화 규칙이 특성 흐름을 적용합니다.

위 그림을 보면 규칙이 **msExchMasterAccountSid**(Exchange) 및 **msRTCSIP-OriginatorSid**(Lync)를 사용하여 **objectSID**에 조인하려는 시도를 확인할 수 있으며 이는 계정 리소스 포리스트 토폴로지에서 발생할 것으로 예상됩니다. 모든 포리스트에서 동일한 규칙을 발견합니다. 즉, 모든 포리스트가 계정 또는 리소스 포리스트일 수 있다고 가정합니다. 또한 이 구성은 단일 포리스트에 작동되고 조인할 수 없는 계정이 있는 경우에 작동합니다.

#### <a name="transformations"></a>변환
변환 섹션은 개체가 조인되고 범위 지정 필터를 충족하는 경우 대상 개체에 적용되도록 모든 특성 흐름을 정의합니다. **AD에서 들어오기 – 사용자 AccountEnabled** 동기화 규칙으로 돌아가서 다음 변환을 찾습니다.

![동기화 규칙 편집기의 변환 탭 ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

이 구성을 컨텍스트에 배치하려면 계정 리소스 포리스트 배포에서 Exchange 및 Lync 설정을 사용하여 계정 포리스트에서 활성화된 계정을 찾고 리소스 포리스트에서 비활성화된 계정을 찾을 예정입니다. 찾고 있는 동기화 규칙은 로그인에 필요한 특성을 포함하며 해당 특성은 활성화된 계정이 있는 포리스트에서부터 진행되어야 합니다. 이러한 모든 특성 흐름은 하나의 동기화 규칙에 함께 배치됩니다.

변환은 다른 형식을 가질 수 있습니다: 상수, 직접 및 식.

* 일정한 흐름은 항상 하드 코딩된 값을 전달합니다. 위의 경우 **accountEnabled**라는 메타버스 특성에서 값을 항상 **True**로 설정합니다.
* 직접 흐름은 원본에 있는 특성의 값을 대상 특성에 항상 있는 그대로 전달합니다.
* 세 번째 흐름 형식은 표현식으로, 더 많은 고급 구성에 사용됩니다.

표현식 언어는 VBA(Visual Basic for Applications)로 Microsoft Office 또는 VBScript의 경험을 가진 사람은 형식을 인식할 수 있습니다. 특성은 대괄호로 묶입니다(예: [attributeName]). 특성 이름 및 함수 이름은 대/소문자를 구분하지만 동기화 규칙 편집기는 표현식을 평가하여 표현식이 유효하지 않은 경우 경고를 제공합니다. 모든 표현식은 함수가 중첩된 단일 행으로 표현됩니다. 다음은 구성 언어의 강력한 기능을 표시하기 위해 삽입된 추가 주석이 있는 pwdLastSet에 대한 흐름입니다.

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

특성 흐름에 대한 식 언어의 자세한 내용은 [선언적 프로비전 식 이해](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) 를 참조하세요.

### <a name="precedence"></a>우선 순위
일부 개별 동기화 규칙을 살펴보았지만 규칙은 구성에서 함께 작동합니다. 일부 경우에 특성 값은 여러 동기화 규칙에서 동일한 대상 특성에 기여합니다. 이 경우 특성 우선 순위는 어떤 특성이 이기는지를 결정하는 데 사용됩니다. 예를 들어 특성 sourceAnchor를 살펴봅니다. 이 특성은 Azure AD에 로그인할 수 있는 중요한 특성입니다. **AD에서 들어오기 – 사용자 AccountEnabled** 및 **AD에서 들어오기 – 사용자 공통**이라는 다른 두 가지 동기화 규칙에서 이 특성에 대한 특성 흐름에 찾을 수 있습니다. 동기화 규칙 우선 순위로 인해 메타버스 개체에 조인하는 개체가 있는 경우 sourceAnchor 특성은 사용하도록 설정된 계정을 사용하는 포리스트에서 먼저 제공됩니다. 활성화된 계정이 없는 경우 동기화 엔진은 일반 동기화 규칙 **AD에서 들어오기 – 일반적인 사용자**를 사용합니다. 이 구성은 비활성화된 계정에 대해서도 여전히 sourceAnchor가 있다는 것을 확인합니다.

![동기화 규칙 인바운드](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

동기화 규칙에 대한 우선 순위는 설치 마법사에 의해 그룹에 설정됩니다. 그룹의 모든 규칙은 동일한 이름을 갖지만 다른 연결된 디렉터리에 연결됩니다. 설치 마법사는 **AD에서 들어오기 – 사용자 조인** 규칙에 가장 높은 우선순위를 제공하고 모든 연결된 AD 디렉터리를 반복합니다. 그런 다음 미리 정의된 순서에 따라 규칙의 다음 그룹을 계속 진행합니다. 그룹 내에서 규칙은 마법사에 추가된 커넥터의 순서로 추가됩니다. 다른 커넥터가 마법사를 통해 추가되면 동기화 규칙은 다시 정렬되고 새 커넥터의 규칙은 각 그룹에 마지막으로 삽입됩니다.

### <a name="putting-it-all-together"></a>모든 항목 요약
이제 다른 동기화 규칙을 사용하여 구성이 작동하는 방식을 이해할 수 있을 만큼 동기화 규칙에 대해 충분히 알고 있습니다. 메타버스에도 제공된 사용자와 특성을 보면 다음과 같은 순서로 규칙이 적용됩니다.

| Name | 주석 |
|:--- |:--- |
| AD에서 들어오기 – 사용자 조인 |메타버스를 사용하여 커넥터 공간 개체에 조인시키기 위한 규칙. |
| AD에서 들어오기 – 사용하도록 설정된 UserAccount |Azure AD 및 Office 365에 로그인하는 데 필요한 속성. 사용된 계정에서 이러한 특성이 필요합니다. |
| AD에서 들어오기 – Exchange에서 사용자 공통 |전체 주소 목록에 있는 특성. 사용자의 사서함이 위치한 포리스트에서 데이터 품질이 가장 훌륭하다고 가정합니다. |
| AD에서 들어오기 – 사용자 공통 |전체 주소 목록에 있는 특성. 사서함을 찾지 못한 경우에 다른 조인된 개체가 특성 값을 제공할 수 있습니다. |
| AD에서 들어오기 – 사용자 Exchange |Exchange가 감지되는 경우에만 존재합니다. 인프라 Exchange 특성을 전송합니다. |
| AD에서 들어오기 – 사용자 Lync |Lync가 감지되는 경우에만 존재합니다. 모든 인프라 Lync 특성을 전송합니다. |

## <a name="next-steps"></a>다음 단계
* [선언적 프로비전 이해](active-directory-aadconnectsync-understanding-declarative-provisioning.md)에서 구성 모델에 대해 자세히 알아봅니다.
* [선언적 프로비전 식 이해](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)에서 식 언어에 대해 자세히 알아봅니다.
* 기본 구성이 [사용자 및 연락처 이해](active-directory-aadconnectsync-understanding-users-and-contacts.md)
* [기본 구성으로 변경하는 방법](active-directory-aadconnectsync-change-the-configuration.md)에서 선언적 프로비전을 사용하여 실용적으로 변경하는 방법을 참조하세요.

**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

