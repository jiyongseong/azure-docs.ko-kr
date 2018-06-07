---
title: "Azure AD Connect 동기화 서비스 섀도 특성 | Microsoft Docs"
description: "Azure AD Connect 동기화 서비스에서 섀도 특성이 작동하는 방식을 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 19d5ae46955ecc094c340d141485d3eb54c8e9b2
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect 동기화 서비스 섀도 특성
대부분의 특성은 온-프레미스 Active Directory에 있을 때와 동일한 방식으로 Azure AD에 표현됩니다. 하지만 일부 특성은 조작 방법이 특별하며 Azure AD의 특성 값이 Azure AD Connect가 동기화하는 값과 다를 수 있습니다.

## <a name="introducing-shadow-attributes"></a>섀도 특성 소개
일부 특성은 Azure AD에서 두 가지로 표현됩니다. 온-프레미스 값과 계산된 값이 모두 저장됩니다. 이러한 추가 특성을 섀도 특성이라고 합니다. 이 동작을 볼 수 있는 가장 일반적인 특성 두 개는 **userPrincipalName** 및 **proxyAddress**입니다. 이러한 특성에 확인되지 않은 도메인을 나타내는 값이 있으면 특성 값의 변화가 발생합니다. 하지만 Connect의 동기화 엔진이 섀도 특성의 값을 읽기 때문에 이러한 관점에서 Azure AD에 의해 해당 특성이 확인됩니다.

Azure Portal 또는 PowerShell을 사용하여 섀도 특성을 볼 수는 없습니다. 하지만 개념을 이해하면 온-프레미스와 클라우드의 특성 값이 서로 다른 시나리오를 해결하는 데 도움이 됩니다.

이 동작을 보다 정확하게 이해하려면 Fabrikam의 예를 살펴보세요.  
![도메인](./media/active-directory-aadconnectsyncservice-shadow-attributes/domains.png)  
온-프레미스 Active Directory에 UPN 접미사가 여러 개 있지만 확인된 것은 하나뿐입니다.

### <a name="userprincipalname"></a>userPrincipalName
사용자는 유효성이 확인되지 않은 도메인에서 다음과 같은 특성 값을 가집니다.

| 특성 | 값 |
| --- | --- |
| 온-프레미스 userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

userPrincipalName 특성은 PowerShell을 사용할 때 표시되는 값입니다.

실제 온-프레미스 특성 값이 Azure AD에 저장되므로 사용자가 fabrikam.com domain을 확인할 때 Azure AD는 shadowUserPrincipalName의 값으로 userPrincipalName 특성을 업데이트합니다. 사용자는 이러한 값을 업데이트하기 위해 Azure AD Connect의 변경 내용을 동기화할 필요가 없습니다.

### <a name="proxyaddresses"></a>proxyAddresses
proxyAddresses에서도 확인된 도메인만 포함하기 위한 동일한 프로세스가 발생하지만 몇 가지 추가 논리가 있습니다. 확인된 도메인 검사가 사서함 사용자에 대해서만 발생합니다. 메일이 활성화된 사용자 또는 연락처는 다른 Exchange 조직의 사용자를 나타내며 고객은 proxyAddresses의 값을 이러한 개체에 추가할 수 있습니다.

사서함 사용자의 경우 온-프레미스 또는 Exchange Online에서 확인된 도메인의 값만 표시됩니다. 다음과 같이 표시될 수 있습니다.

| 특성 | 값 |
| --- | --- |
| 온-프레미스 proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

이 경우 해당 도메인이 확인되지 않았기 때문에 **smtp:abbie.spencer@fabrikam.com**이 제거되었습니다. 하지만 Exchange가 **SIP:abbie.spencer@fabrikamonline.com**을 추가했습니다. Fabrikam은 Lync/Skype 온-프레미스를 사용하지 않지만 Azure AD와 Exchange Online은 그에 대한 준비를 합니다.

proxyAddresses에 대한 이 논리를 **ProxyCalc**라고 합니다. 다음과 같은 경우 사용자에 대한 모든 변경 내용과 함께 ProxyCalc가 호출됩니다.

- 사용자가 Exchange를 사용하도록 허가되지 않은 경우에도 Exchange Online을 포함하는 서비스 계획이 사용자에게 할당되었습니다. 사용자에게 Office E3 SKU가 할당되었지만 SharePoint Online만 할당된 경우를 예로 들 수 있습니다. 사서함이 계속 온-프레미스에 있는 경우에도 마찬가지입니다.
- msExchRecipientTypeDetails 특성에 값이 있습니다.
- 사용자가 proxyAddresses 또는 userPrincipalName을 변경합니다.

ProxyCalc는 사용자에 대한 변경 내용을 처리하는 데 다소 시간이 걸릴 수 있으며 Azure AD Connect 내보내기 프로세스와 동기화되지 않습니다.

> [!NOTE]
> ProxyCalc 논리에는 이 토픽에서 설명하지 않은 고급 시나리오를 위한 몇 가지 추가 동작이 있습니다. 이 토픽은 동작의 이해를 돕기 위해 제공되었을 뿐, 모든 내부 논리를 설명하지는 않습니다.

### <a name="quarantined-attribute-values"></a>격리된 특성 값
중복된 특성 값이 있는 경우에도 섀도 특성이 사용됩니다. 자세한 내용은 참조 [중복 특성 복원력](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)을 참조하세요.

## <a name="see-also"></a>참고 항목
* [Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
