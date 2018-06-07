---
title: Azure AD Privileged Identity Management 보안 마법사
description: Azure Active Directory Privileged Identity Management 확장을 처음 실행하면 보안 마법사가 표시됩니다. 이 문서는 마법사를 사용하는 단계를 설명합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 02/27/2017
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.openlocfilehash: 4b3856d74b1109b20a1ff9f93b76ee36b66ee312
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="using-the-security-wizard-in-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management에서 보안 마법사 사용 
조직에 대해 Azure PIM(Privileged Identity Management)을 처음 실행하면 마법사가 표시됩니다. 마법사는 권한 있는 ID에 대한 보안 위험을 이해하고 위험을 줄이도록 PIM을 사용하는 방법을 이해하도록 도와줍니다. 나중에 작업하려는 경우 마법사에서 기존 역할 할당을 변경할 필요가 없습니다.

## <a name="what-to-expect"></a>예상 프로그램
조직에서 PIM 사용을 시작하기 전에 모든 역할 할당은 영구적입니다. 현재 해당 권한이 필요하지 않는 경우에도 사용자는 항상 이러한 역할에 할당되어 있습니다.  마법사의 첫 번째 단계에서는 권한이 높은 역할의 목록과 얼마나 많은 사용자가 현재 해당 역할에 있는지를 보여 줍니다. 특정 역할을 자세히 알아보면 익숙하지 않은 사용자에 대해 더 잘 알 수 있습니다.

마법사의 두 번째 단계는 관리자의 역할 할당을 변경할 기회를 제공합니다.  

> [!WARNING]
> Microsoft 계정이 아니라 조직 계정을 사용하는 적어도 한 명의 전역 관리자와 둘 이상의 권한 있는 역할 관리자가 있는 것이 중요합니다. 권한 있는 역할 관리자가 한 명인 경우 해당 계정이 삭제되면 조직은 PIM을 관리할 수 없습니다.
> 또한 사용자가 Microsoft 계정 (Skype나 Outlook.com과 같은 Microsoft 서비스에 로그인 하는 데 사용하는 계정)을 가진 경우 역할 할당을 영구적으로 유지합니다. 해당 역할의 활성화에 대해 MFA를 요구하려는 경우 해당 사용자가 잠기게 됩니다.
> 
> 

항목을 변경한 후에는 마법사가 표시되지 않습니다. 다음에 사용자 또는 다른 권한 있는 역할 관리자가 PIM을 사용할 때 PIM 대시보드가 표시됩니다.  

* 역할에서 사용자를 추가 또는 제거하거나 영구에서 적격으로 할당을 변경하려면 [사용자의 역할을 추가 또는 제거하는 방법](active-directory-privileged-identity-management-how-to-add-role-to-user.md)을 참조하세요.
* 더 많은 사용자에게 PIM을 관리하기 위해 액세스 권한을 제공하려면 [PIM을 관리하기 위해 액세스 권한을 제공하는 방법](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

