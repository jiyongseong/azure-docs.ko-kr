---
title: 로컬로 관리되는 파트너 계정에게 Azure AD B2B 사용자로 클라우드 리소스에 대한 액세스 권한 부여 | Microsoft Docs
description: Azure AD B2B 공동 작업과 동일한 자격 증명을 사용하여 로컬로 관리되는 외부 파트너에게 로컬 및 클라우드 리소스에 대한 액세스 권한을 부여합니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/24/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 71d5ce8728d876740d6ef00b55ecdc9232a06f80
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267227"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Azure AD B2B 공동 작업을 사용하여 로컬로 관리되는 파트너 계정에게 클라우드 리소스에 대한 액세스 권한 부여

Azure AD(Azure Active Directory) 이전에는 온-프레미스 ID 시스템이 있는 조직은 일반적으로 온-프레미스 디렉터리에서 파트너 계정을 관리해왔습니다. 이러한 조직에서 Azure AD에 앱을 이동하기 시작하면 파트너가 필요한 리소스에 액세스할 수 있도록 해야 합니다. 리소스가 온-프레미스에 있는지 아니면 클라우드에 있는지 여부는 중요하지 않습니다. 또한 파트너 사용자가 온-프레미스와 Azure AD 리소스 모두에 대해 동일한 로그인 자격 증명을 사용할 수 있도록 해야 합니다. 

온-프레미스 디렉터리에 외부 파트너에 대한 계정을 만드는 경우(예를 들어 partners.contoso.com 도메인에서 Wendy Moran이란 외부 사용자를 위한 “wmoran”이란 로그인 이름의 계정을 하나 만드는 경우) 이제 이러한 계정을 클라우드에 동기화할 수 있습니다. 특히 Azure AD Connect를 사용하여 클라우드에 대한 파트너 계정을 Azure AD B2B 사용자(즉, UserType이 게스트인 사용자)로 동기화할 수 있습니다. 그러면 파트너 사용자에게 필요 이상의 많은 액세스 권한을 제공하지 않으면서 파트너 사용자가 로컬 계정과 동일한 자격 증명을 사용하여 클라우드 리소스에 액세스하도록 할 수 있습니다. 

## <a name="identify-unique-attributes-for-usertype"></a>UserType에 대한 고유한 특성 식별

UserType 특성의 동기화를 사용하도록 설정하기 전에 먼저 온-프레미스 Active Directory에서 UserType 특성을 파생시키는 방법을 결정해야 합니다. 즉, 외부 협력자에 대해 고유한 온-프레미스 환경의 매개 변수를 알아봅니다. 해당 조직의 멤버와 이러한 외부 협력자를 구별하는 매개 변수를 확인합니다.

이를 위한 두 가지 일반적인 방법은 다음과 같습니다.

- 사용하지 않는 온-프레미스 Active Directory 특성(예: extensionAttribute1)을 원본 속성으로 사용하도록 지정합니다. 
- 또는 UserType 특성의 값을 다른 속성에서 파생시킵니다. 예를 들어 온-프레미스 Active Directory UserPrincipalName 특성이 도메인 *@partners.contoso.com*로 끝나는 경우 모든 사용자를 게스트로 동기화하는 것이 좋습니다.
 
자세한 특성 요구 사항은 [UserType의 동기화 사용](../connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)을 참조하세요. 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>클라우드로 사용자를 동기화하도록 Azure AD Connect 구성

고유한 특성을 식별한 다음 클라우드에 이러한 사용자를 Azure AD B2B 사용자로 동기화하도록 Azure AD Connect를 구성할 수 있습니다(즉, UserType이 게스트인 사용자). 권한 부여라는 관점에서 이러한 사용자는 Azure AD B2B 공동 작업 초대 프로세스를 통해 만든 B2B 사용자와 구분될 수 없습니다.

구현 지침은 [UserType의 동기화 사용](../connect/active-directory-aadconnectsync-change-the-configuration.md#enable-synchronization-of-usertype)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [하이브리드 조직에 대한 Azure Active Directory B2B 공동 작업](hybrid-organizations.md)
- [Azure AD의 B2B 사용자에게 온-프레미스 응용 프로그램에 대한 액세스 권한 부여](hybrid-cloud-to-on-premises.md)
- Azure AD Connect 개요는 [Azure Active Directory와 온-프레미스 디렉터리 통합](../connect/active-directory-aadconnect.md)을 참조하세요.

