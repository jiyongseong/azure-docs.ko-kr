---
title: Azure Active Directory에서 셀프 서비스 응용 프로그램 액세스 관리 설정 | Microsoft Docs
description: Azure Active Directory에서 보안 그룹 또는 Office 365 그룹을 만들고 관리하고 보안 그룹 또는 Office 365 그룹 멤버 자격을 요청합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal;it-pro;
ms.openlocfilehash: ee116762945f01c5c8031b97e0e09fa6c6ae6d7f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="set-up-azure-active-directory-for-self-service-group-management"></a>셀프 서비스 그룹 관리를 위한 Azure Active Directory 설정
사용자가 Azure AD(Azure Active Directory)에서 보안 그룹 또는 Office 365 그룹을 만들고 관리할 수 있습니다. 사용자는 보안 그룹 또는 Office 365 그룹 멤버 자격을 요청할 수도 있고 그룹의 소유자는 멤버 자격을 수락하거나 거부할 수 있습니다. 그룹 멤버 자격에 대한 일상적인 제어를 해당 멤버 자격에 대한 비즈니스 컨텍스트를 이해하는 사람에게 위임할 수 있습니다. 셀프 서비스 그룹 관리 기능은 보안 그룹 및 Office 365 그룹에 대해서만 사용할 수 있지만 메일 사용 가능 보안 그룹 및 메일 그룹에는 사용할 수 없습니다.

셀프 서비스 그룹 관리는 현재 위임된 그룹 관리 및 셀프 서비스 그룹 관리 등 두 개의 중요한 시나리오를 제공합니다.

* **위임된 그룹 관리** 회사에서 사용하고 있는 SaaS 응용 프로그램에 대한 액세스를 관리하는 관리자를 예로 들 수 있습니다. 이러한 액세스 권한 관리가 번거로워지자 관리자는 비즈니스 소유자에게 요청하여 새 그룹을 만듭니다. 관리자는 새 그룹에 응용 프로그램에 대한 액세스를 할당하고 이미 응용 프로그램에 액세스할 권한이 있는 모든 사용자 그룹에 추가합니다. 그러면 비즈니스 소유자가 더 많은 사용자를 추가할 수 있으며 해당 사용자는 응용 프로그램에 자동으로 프로비전됩니다. 비즈니스 소유자는 사용자에 대한 액세스를 관리하는 관리자를 대기할 필요가 없습니다. 관리자가 다른 비즈니스 그룹의 관리자에게 동일한 사용 권한을 부여하는 경우 해당 사용자는 고유한 사용자에 대한 액세스를 관리할 수도 있습니다. 비즈니스 소유자와 관리자는 모두 다른 사용자를 보거나 관리할 수 없습니다. 관리자는 계속 해당 응용 프로그램에 액세스할 수 있는 모든 사용자를 보고 필요한 경우 액세스 권한을 차단할 수 있습니다.
* **셀프 서비스 그룹 관리** - 이 시나리오의 예제에서는 독립적으로 설정한 SharePoint Online 사이트를 보유한 두 사용자를 가정합니다. 이들은 상대방 팀을 해당 사이트에 액세스할 수 있도록 하려고 합니다. 이를 위해서 두 사용자는 Azure AD에 하나의 그룹을 만들고 각각 SharePoint Online에서 해당 사이트에 대한 액세스 권한을 제공할 그룹을 선택합니다. 사용자가 액세스 권한이 필요하면 액세스 패널에서 요청하고 승인을 받으면 자동으로 두 SharePoint Online 사이트에 대한 액세스 권한을 얻습니다. 이후 사이트에 액세스하는 모든 사용자가 특정 SaaS 응용 프로그램에 대한 액세스 권한도 가져야 하는지를 둘 중 한 명이 결정할 수 있습니다. SaaS 응용 프로그램의 관리자는 응용 프로그램에 대한 액세스 권한을 SharePoint Online 사이트에 추가할 수 있습니다. 이후로 승인된 모든 요청은 두 SharePoint Online 사이트 및 해당 SaaS 응용 프로그램에 대한 액세스 권한을 부여합니다.

## <a name="make-a-group-available-for-user-self-service"></a>사용자 셀프서비스에 사용할 수 있는 그룹 만들기
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. **사용자 및 그룹**을 선택한 다음 **그룹 설정**을 선택합니다.
3. **셀프 서비스 그룹 관리 사용**을 **예**로 설정합니다.
4. **사용자가 보안 그룹을 만들 수 있음** 또는 **사용자가 Office 365 그룹을 만들 수 있음**을 **예**로 설정합니다.
  * 이러한 설정을 사용하도록 설정하면 디렉터리의 모든 사용자가 새 보안 그룹을 만들고 이러한 그룹에 멤버를 추가할 수 있습니다. 이러한 새 그룹은 다른 모든 사용자에 대한 액세스 패널에도 표시됩니다. 그룹에 대한 정책 설정에서 허용하는 경우 다른 사용자가 이러한 그룹에 가입하도록 요청할 수 있습니다. 
  * 이러한 설정을 사용하지 않도록 설정한 경우 사용자 그룹을 만들 수 없고 기존 그룹의 소유자를 변경할 수 없습니다. 그러나 여전히 해당 그룹의 구성원을 관리하고 다른 사용자의 해당 그룹 가입 요청을 승인할 수 있습니다.

또한 **보안 그룹을 관리할 수 있는 사용자** 및 **Office 365 그룹을 관리할 수 있는 사용자**를 사용하여 사용자의 셀프 서비스 그룹 관리에 대한 액세스를 더 세밀하게 제어할 수 있습니다. **사용자가 그룹을 만들 수 있음**을 사용하도록 설정하면 테넌트의 모든 사용자가 새 그룹을 만들고 이러한 그룹에 멤버를 추가할 수 있습니다. **일부**로 설정하여 제한된 사용자의 그룹으로 그룹 관리를 제한합니다. 이 스위치가 **일부**로 설정되면 새 그룹을 만들고 멤버를 추가하기 전에 SSGMSecurityGroupsUsers 그룹에 사용자를 추가해야 합니다. **보안 그룹에 대해 셀프 서비스를 사용할 수 있는 사용자** 및 **Office 365 그룹을 관리할 수 있는 사용자**를 **모두**로 설정하면 테넌트의 모든 사용자가 새 그룹을 만들 수 있습니다.

**보안 그룹을 관리할 수 있는 그룹** 또는 **Office 365 그룹을 관리할 수 있는 그룹**을 사용하여 단일 그룹의 멤버가 셀프 서비스를 사용할 수 있도록 지정할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)
* [그룹 설정을 구성하는 Azure Active Directory cmdlets](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory란?](active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
