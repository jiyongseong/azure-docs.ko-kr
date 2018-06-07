---
title: "Azure Active Directory Domain Services: 관리되는 도메인에서의 동기화 | Microsoft Docs"
description: "Azure Active Directory Domain Services 관리되는 도메인에서의 동기화를 이해합니다."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 5c324ea5e268d97134202eff6e96764bedc6ca75
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리되는 도메인에서 동기화
다음 다이어그램에서는 Azure AD 도메인 서비스 관리되는 도메인에서 동기화가 작동하는 방식을 보여 줍니다.

![Azure AD Domain Services의 동기화 토폴로지](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>온-프레미스 디렉터리에서 Azure AD 테넌트로 동기화
Azure AD Connect 동기화는 사용자 계정, 그룹 구성원 자격 및 자격 증명 해시를 Azure AD 테넌트에 동기화하는 데 사용됩니다. UPN 및 온-프레미스 SID(보안 식별자)와 같은 사용자 계정의 특성이 동기화됩니다. Azure AD Domain Services를 사용하는 경우 NTLM 및 Kerberos 인증에 필요한 기존 자격 증명 해시도 Azure AD 테넌트에 동기화됩니다.

쓰기 저장을 구성한 경우 Azure AD 디렉터리에서 발생한 변경 내용이 온-프레미스 Active Directory에 다시 동기화됩니다. 예를 들어 Azure AD의 셀프 서비스 암호 변경 기능을 사용하여 암호를 변경한 경우 변경된 암호가 온-프레미스 AD 도메인에서 업데이트됩니다.

> [!NOTE]
> 모든 알려진 버그에 대한 수정 프로그램을 유지할 수 있도록 항상 최신 버전의 Azure AD Connect를 사용합니다.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Azure AD 테넌트에서 관리되는 도메인으로 동기화
사용자 계정, 그룹 구성원 자격 및 자격 증명 해시는 Azure AD 테넌트에서 Azure AD Domain Services 관리되는 도메인으로 동기화됩니다. 이 동기화 프로세스는 자동입니다. 이 동기화 프로세스는 구성, 모니터링 또는 관리할 필요가 없습니다. 디렉터리의 일회성 초기 동기화가 완료되면 Azure AD에 적용된 변경 사항이 관리되는 도메인에 반영되는 데 일반적으로 약 20분이 소요됩니다. 이 동기화 간격은 Azure AD에서의 암호 변경 또는 속성 변경에 적용됩니다.

또한 동기화 프로세스는 본질적으로 단방향입니다. 관리되는 도메인은 사용자가 만든 사용자 지정 OU를 제외하고는 대부분 읽기 전용입니다. 따라서 관리되는 도메인 내에서는 사용자 특성, 사용자 암호 또는 그룹 구성원 자격을 변경할 수 없습니다. 그렇기 때문에 관리되는 도메인에서 Azure AD 테넌트로 변경 내용의 역방향 동기화는 없습니다.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>다중 포리스트 온-프레미스 환경에서의 동기화
많은 조직에는 여러 계정 포리스트로 구성된 매우 복잡한 온-프레미스 ID 인프라가 있습니다. Azure AD Connect는 다중 포리스트 환경에서 Azure AD 테넌트로의 사용자, 그룹 및 자격 증명 해시 동기화를 지원합니다.

반면, Azure AD 테넌트는 훨씬 간단한 단일 구조 네임스페이스입니다. 사용자가 Azure AD에서 보호되는 응용 프로그램에 안정적으로 액세스할 수 있도록 하려면 서로 다른 포리스트의 사용자 계정 간에 발생하는 UPN 충돌을 해결해야 합니다. Azure AD 도메인 서비스 관리되는 도메인은 Azure AD 테넌트와 매우 유사합니다. 따라서 관리되는 도메인의 OU 구조는 단일 구조입니다. 모든 사용자 및 그룹이 동기화된 온-프레미스 도메인 또는 포리스트에 관계없이 'AADDC 사용자' 컨테이너 내에 저장됩니다. 사용자가 온-프레미스에 계층형 OU 구조를 구성했을 수도 있지만 이 경우에도 관리되는 도메인은 단순한 단일 OU 구조를 유지합니다.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>예외 - 관리되는 도메인에 동기화되지 않는 항목
다음 개체 또는 특성은 Azure AD 테넌트나 관리되는 도메인에 동기화되지 않습니다.

* **제외된 특성:** Azure AD Connect를 사용하여 온-프레미스 도메인에서 Azure AD 테넌트로의 동기화에서 특정 특성을 제외할 수 있습니다. 이러한 제외된 특성은 관리되는 도메인에서 사용할 수 없습니다.
* **그룹 정책:** 온-프레미스 도메인에 구성된 그룹 정책은 관리되는 도메인에 동기화되지 않습니다.
* **SYSVOL 공유:** 마찬가지로 온-프레미스 도메인의 SYSVOL 공유 내용은 관리되는 도메인에 동기화되지 않습니다.
* **컴퓨터 개체:** 온-프레미스 도메인에 가입된 컴퓨터에 대한 컴퓨터 개체는 관리되는 도메인에 동기화되지 않습니다. 이러한 컴퓨터는 관리되는 도메인과 트러스트 관계가 없으며 온-프레미스 도메인에만 속합니다. 관리되는 도메인에서는 관리되는 도메인에 명시적으로 가입한 컴퓨터에 대한 컴퓨터 개체만 찾을 수 있습니다.
* **사용자 및 그룹에 대한 SidHistory 특성:** 온-프레미스 도메인의 기본 사용자 및 기본 그룹 SID는 관리되는 도메인에 동기화됩니다. 그러나 사용자 및 그룹에 대한 기존 SidHistory 특성은 온-프레미스 도메인에서 관리되는 도메인으로 동기화되지 않습니다.
* **OU(조직 구성 단위) 구조:** 온-프레미스 도메인에 정의된 조직 구성 단위는 관리되는 도메인에 동기화되지 않습니다. 관리되는 도메인에는 두 개의 기본 제공 OU가 있습니다. 기본적으로 관리되는 도메인은 단일 OU 구조를 가집니다. 그러나 [관리되는 도메인에서 사용자 지정 OU를 만들](active-directory-ds-admin-guide-create-ou.md) 수 있습니다.

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>관리되는 도메인에 특정 특성이 동기화되는 방식
다음 표에는 몇 가지 공통 특성이 나열되어 있으며 이러한 특성이 관리되는 도메인에 동기화되는 방식이 설명되어 있습니다.

| 관리되는 도메인의 특성 | 원본 | 참고 사항 |
|:--- |:--- |:--- |
| UPN |Azure AD 테넌트의 사용자 UPN 특성 |Azure AD 테넌트의 UPN 특성은 있는 그대로 관리되는 도메인에 동기화됩니다. 따라서 관리되는 도메인에 로그인하는 가장 안정적인 방법은 UPN을 사용하는 것입니다. |
| SAMAccountName |Azure AD 테넌트에서 사용되거나 자동 생성된 사용자의 mailNickname |SAMAccountName 특성은 Azure AD 테넌트의 mailNickname 특성에서 제공됩니다. 여러 사용자 계정에 동일한 mailNickname 특성이 있으면 SAMAccountName이 자동으로 생성됩니다. 사용자의 mailNickname 또는 UPN 접두사가 20자 보다 긴 경우에는 SAMAccountName 특성에 대한 20자 제한을 충족하기 위해 SAMAccountName이 자동으로 생성됩니다. |
| 암호 |Azure AD 테넌트의 사용자 암호 |Kerberos 인증에 필요한 자격 증명 해시(보조 자격 증명이라고도 함)는 Azure AD 테넌트에서 동기화됩니다. Azure AD 테넌트가 동기화된 테넌트인 경우 이러한 자격 증명은 온-프레미스 도메인에서 제공됩니다. |
| 기본 사용자/그룹 SID |자동 생성 |사용자/그룹 계정에 대한 기본 SID는 관리되는 도메인에서 자동으로 생성됩니다. 이 특성은 온-프레미스 AD 도메인에 있는 개체의 기본 사용자/그룹 SID와 일치하지 않습니다. 이 불일치는 관리되는 도메인에 온-프레미스 도메인과 다른 SID 네임스페이스가 있기 때문입니다. |
| 사용자 및 그룹에 대한 SID 기록 |온-프레미스 기본 사용자 및 그룹 SID |관리되는 도메인의 사용자 및 그룹에 대한 SidHistory 특성은 온-프레미스 도메인의 해당 기본 사용자 또는 그룹 SID와 일치하도록 설정됩니다. 이 기능을 사용하면 리소스 ACL을 다시 만들 필요가 없으므로 온-프레미스 응용 프로그램을 관리되는 도메인으로 보다 쉽게 이동할 수 있습니다. |

> [!NOTE]
> **UPN 형식을 사용하여 관리되는 도메인에 로그인:** 관리되는 도메인의 일부 사용자 계정에 대해 SAMAccountName 특성이 자동으로 생성될 수 있습니다. 여러 사용자가 동일한 mailNickname 특성을 사용하거나 사용자의 UPN 접두사가 너무 긴 경우 이러한 사용자에 대한 SAMAccountName이 자동으로 생성될 수 있습니다. 따라서 SAMAccountName 형식(예: 'CONTOSO100\joeuser')은 도메인에 로그인하는 안정적인 방법이 아닐 수 있습니다. 자동 생성된 사용자의 SAMAccountName은 UPN 접두사와 다를 수 있습니다. 관리되는 도메인에 안정적으로 로그인하려면 UPN 형식(예: ‘joeuser@contoso100.com’)을 사용하세요.
>
>

### <a name="attribute-mapping-for-user-accounts"></a>사용자 계정에 대한 특성 매핑
다음 표에서는 Azure AD 테넌트의 사용자 개체에 대한 특정 특성이 관리되는 도메인의 해당 특성에 동기화되는 방식을 보여 줍니다.

| Azure AD 테넌트의 사용자 특성 | 관리되는 도메인의 사용자 특성 |
|:--- |:--- |
| accountEnabled |userAccountControl(ACCOUNT_DISABLED 비트를 설정하거나 지움) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName(자동 생성되는 경우도 있음) |
| mobile |mobile |
| objectId |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl(DONT_EXPIRE_PASSWORD 비트를 설정하거나 지움) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>그룹에 대한 특성 매핑
다음 표에서는 Azure AD 테넌트의 그룹 개체에 대한 특정 특성이 관리되는 도메인의 해당 특성에 동기화되는 방식을 보여 줍니다.

| Azure AD 테넌트의 그룹 특성 | 관리되는 도메인의 그룹 특성 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName(자동 생성되는 경우도 있음) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectId |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>관리되는 도메인에서 Azure AD 테넌트로 동기화되지 않는 개체
이 문서의 이전 섹션에서 설명한 대로, 관리되는 도메인에서 Azure AD 테넌트로 다시 동기화되지는 않습니다. [관리되는 도메인에서 사용자 지정 OU(조직 구성 단위)를 만들](active-directory-ds-admin-guide-create-ou.md) 수 있습니다. 또한 이러한 사용자 지정 OU 내에서 다른 OU, 사용자, 그룹 또는 서비스 계정을 만들 수 있습니다. 사용자 지정 OU 내에서 만든 개체는 Azure AD 테넌트에 다시 동기화되지 않습니다. 이러한 개체는 관리되는 도메인 내에서만 사용할 수 있습니다. 따라서 Azure AD PowerShell cmdlet, Azure AD Graph API 또는 Azure AD 관리 UI를 사용하여 이러한 개체를 볼 수 없습니다.

## <a name="related-content"></a>관련 콘텐츠
* [기능 - Azure AD Domain Services](active-directory-ds-features.md)
* [배포 시나리오 - Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Azure AD 도메인 서비스에 대한 네트워킹 고려 사항](active-directory-ds-networking.md)
* [Azure AD 도메인 서비스 시작](active-directory-ds-getting-started.md)
