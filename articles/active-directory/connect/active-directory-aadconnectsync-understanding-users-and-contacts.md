---
title: "Azure AD Connect 동기화: 사용자, 그룹 및 연락처 이해 | Microsoft Docs"
description: "Azure AD Connect 동기화의 사용자, 그룹 및 연락처에 대해 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi;andkjell
ms.openlocfilehash: 7f4bc51630653bfe341bfcb5c11699020053585a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect 동기화: 사용자, 그룹 및 연락처 이해
여러 Active Directory 포리스트를 가져야 하는 이유와 배포 토폴로지는 여러 가지가 있습니다. 일반적인 모델에는 합병 & 인수 후 계정 리소스 배포 및 GAL 동기화 포리스트가 포함됩니다. 하지만 순수 모델이 있어도 하이브리드 모델도 일반적입니다. Azure AD Connect 동기화의 기본 구성은 어느 특정 모델을 가정하지는 않지만 설치 가이드에서 사용자 일치가 어떻게 선택되어 있는지에 따라 다양한 동작을 관찰할 수 있습니다.

이 항목에서는 기본 구성이 특정 토폴로지에서 어떻게 동작하는지 살펴보겠습니다. 구성을 살펴보고 동기화 규칙 편집기를 사용하여 구성을 확인할 수 있습니다.

구성을 가정하는 몇 가지 일반적인 규칙이 있습니다.
* 원본 Active Directory에서 가져오는 순서와 관계없이 최종 결과는 항상 동일해야 합니다.
* 활성 계정은 항상 **userPrincipalName** 및**sourceAnchor**를 포함한 로그인 정보를 제공합니다.
* 활성화된 계정이 없는 경우 비활성화된 계정이 연결된 사서함이 아니라면 userPrincipalName 및 sourceAnchor를 제공합니다.
* 연결된 사서함이 있는 계정은 userPrincipalName 및 sourceAnchor에 대 해 사용되지 않습니다. 활성 계정이 나중에 검색되는 것으로 가정합니다.
* 연락처 개체를 Azure AD에 사용자 또는 연락처로 프로비전할 수 있습니다. 모든 원본 Active Directory 포리스트가 처리 될 때까지 실제로 알지 못합니다.

## <a name="groups"></a>그룹
Active Directory에서 Azure AD로 그룹을 동기화할 때 다음에 유의해야 합니다.

* Azure AD Connect는 기본 제공 보안 그룹을 디렉터리 동기화에서 제외합니다.

* Azure AD Connect는 [주 그룹 구성원 자격](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx)에서 Azure AD로의 동기화를 지원하지 않습니다.

* Azure AD Connect는 [동적 배포 그룹 구성원 자격](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx)에서 Azure AD로의 동기화를 지원하지 않습니다.

* Active Directory 그룹을 Azure AD에 메일 사용이 가능한 그룹으로 동기화하려면

    * 그룹의 *proxyAddress* 속성이 비어 있는 경우 *mail* 속성에 값이 있어야 합니다.

    * 그룹의 *proxyAddress* 특성이 비어 있지 않은 경우 SMTP 프록시 주소 값이 하나 이상 포함되어야 합니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.
    
      * proxyAddress 속성의 값이 *{"X500:/0=contoso.com/ou=users/cn=testgroup"}*인 Active Directory 그룹은 Azure AD에서 메일을 사용할 수 없습니다. SMTP 주소가 없습니다.
      
      * proxyAddress 속성의 값이 *{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe@contoso.com"}*인 Active Directory 그룹은 Azure AD에서 메일을 사용할 수 있습니다.
      
      * proxyAddress 속성의 값이 *{"X500:/0=contoso.com/ou=users/cn=testgroup","smtp:johndoe@contoso.com"}*인 Active Directory 그룹은 Azure AD에서 메일을 사용할 수도 있습니다.

## <a name="contacts"></a>연락처
연락처가 다른 포리스트의 사용자를 나타내게 하는 것은 GALSync 솔루션이 둘 이상의 Exchange 포리스트 사이를 연결하는 M&A 후에 일반적입니다. 연락처 개체는 항상 메일 특성을 사용하여 커넥터 공간에서 메타 버스로 조인됩니다. 이미 연락처 개체나 동일한 메일 주소를 가진 사용자 개체가 있다면 이들 개체가 함께 조인됩니다. 이것은 **In from AD – 연락처 조인**규칙에서 구성됩니다. **AD에서 들어오기 – 연락처 일반**이라는 규칙도 있는데 **sourceObjectType** 메타 버스 특성에 대한 특성 흐름과 **연락처** 상수를 가지고 있습니다. 이 규칙은 우선 순위가 매우 낮으므로 어떤 사용자 개체가 동일한 메타버스 개체에 조인된 경우에는 **In from AD – 사용자 일반** 규칙이 User 값을 이 특성에 제공합니다. 이 규칙을 사용할 경우 조인된 사용자가 없으면 이 특성이 연락처 값을 갖게 되며, 최소 1명의 사용자가 발견되면 사용자 값을 갖습니다.

Azure AD에 개체를 프로비전하는 경우에는 메타버스 특성 **sourceObjectType**이 **연락처**로 설정되면 아웃바운드 규칙 **AAD로 나가기 – 연락처 조인**이 연락처 개체를 생성합니다. 이 특성이 **User**로 설정되면 **AAD로 나가기 – 사용자 조인** 규칙이 대신 사용자 개체를 만듭니다.
추가 원본 Active Directory를 가져와서 동기화하면 개체가 사용자에게 연락처로부터 승격될 수도 있습니다.

예를 들어 GALSync 토폴로지에서 첫번째 포리스트를 가져올 때 두번째 포리스트의 모두를 위해 연락처 개체를 찾습니다. 그러면 AAD 커넥터에 새 연락처 개체를 준비합니다. 나중에 두번째 포리스트를 가져오고 동기화할 때 실제 사용자를 찾아서 기존 메타 버스 개체에 조인시킵니다. 그런 다음 AAD에서 연락처 개체를 삭제하고 대신 새 사용자 개체를 만듭니다.

사용자가 연락처로 표시되는 토폴로지의 경우 반드시 설치 가이드의 메일 특성에 대해 사용자를 일치시키도록 선택해야 합니다. 다른 옵션을 선택하면 주문에 종속된 구성이 만들어집니다. 연락처 개체는 항상 메일 특성에 조인되지만 설치 가이드에서 이 옵션을 선택한 경우에는 사용자 개체가 메일 특성에만 조인됩니다. 그러면 사용자 개체 전에 연락처 개체를 가져온 경우 메타 버스 내의 2개의 다른 개체가 동일한 메일 특성을 갖게 될 수 있습니다. Azure AD로 내보내기 중 오류가 발생합니다. 이 동작은 디자인에 의한 것이며 잘못된 데이터 또는 설치 중에 토폴로지가 올바르게 식별되지 않았음을 나타냅니다.

## <a name="disabled-accounts"></a>비활성화된 계정
비활성화된 계정도 Azure AD에 동기화됩니다. 비활성화된 계정은 Exchange 내 리소스를 나타내는 것이 일반적입니다(예: 회의실). 예외는 앞서 언급한 것처럼 연결된 사서함이 있는 사용자이며, 이것은 Azure AD에 계정을 프로비전하지 않습니다.

비활성화된 사용자 계정이 발견된 경우에는 나중에 다른 활성 계정을 찾지 못하며 개체가 발견된 userPrincipalName 및 sourceAnchor가 있는 Azure AD로 프로비전된다고 가정합니다. 다른 활성 계정이 동일한 메타 버스 개체에 조인되는 경우에는 해당 userPrincipalName 및 sourceAnchor가 사용됩니다.

## <a name="changing-sourceanchor"></a>sourceAnchor 변경
개체를 Azure AD로 내보내면 더 이상 sourceAnchor를 변경할 수 없습니다. 개체가 내보내지면 메타버스 특성 **cloudSourceAnchor**가 Azure AD에서 허용한 **sourceAnchor** 값으로 설정됩니다. **sourceAnchor**가 변경되어 **cloudSourceAnchor**와 일치하지 않으면 **AAD로 나가기 – 사용자 조인** 규칙에서 **sourceAnchor 특성이 변경되었습니다**라는 오류가 발생합니다. 이 경우 개체를 다시 동기화하기 전에 동일한 sourceAnchor가 메타 버스에 다시 나타나도록 구성 또는 데이터를 수정해야 합니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure AD Connect Sync: 사용자 지정 동기화 옵션](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

