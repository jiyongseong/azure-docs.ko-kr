---
title: 동적 그룹 및 Azure Active Directory B2B 공동 작업 | Microsoft Docs
description: Azure Active Directory B2B 공동 작업 기능에서 Azure AD 동적 그룹을 사용하는 방법을 보여줍니다.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 12/14/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 18057b71415bea5d5f029db6fe311f76c1a549a1
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267228"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>동적 그룹 및 Azure Active Directory B2B 공동 작업

## <a name="what-are-dynamic-groups"></a>동적 그룹이란?
Azure AD(Azure Active Directory)에 대한 보안 그룹 구성원의 동적 구성은 [Azure Portal](https://portal.azure.com)에서 사용할 수 있습니다. 관리자는 사용자 특성(예: userType, department 또는 country)에 따라 Azure AD에서 만든 그룹을 채우는 규칙을 설정할 수 있습니다. 특성에 따라 구성원을 보안 그룹에 자동으로 추가하거나 보안 그룹에서 제거할 수 있습니다. 이러한 그룹은 SharePoint 사이트, 문서 등의 응용 프로그램 또는 클라우드 리소스에 대한 액세스 권한을 제공하고 구성원에게 라이선스를 할당할 수 있습니다. [Azure Active Directory의 전용 그룹](../active-directory-accessmanagement-dedicated-groups.md)에서 동적 그룹에 대해 자세히 알아보세요.

동적 그룹을 만들고 사용하려면 적절한 [Azure AD Premium P1 또는 P2 라이선스](https://azure.microsoft.com/pricing/details/active-directory/)가 필요합니다. 자세한 내용은 [Azure Active Directory에서 동적 그룹 멤버 자격에 대한 특성 기반 규칙 만들기](../active-directory-groups-dynamic-membership-azure-portal.md) 문서를 참조하세요.

## <a name="what-are-the-built-in-dynamic-groups"></a>기본 제공 동적 그룹이란?
**모든 사용자** 동적 그룹을 사용하면 테넌트 관리자는 클릭 한 번으로 테넌트의 모든 사용자가 포함된 그룹을 만들 수 있습니다. 기본적으로 **모든 사용자** 그룹은 구성원 및 게스트를 비롯하여 디렉터리의 모든 사용자를 포함합니다.
새 Azure Active Directory 관리 포털 내의 그룹 설정 보기에서 **모든 사용자**를 사용하도록 설정할 수 있습니다.

![예로 설정된 모든 사용자 그룹 사용 표시](media/use-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>모든 사용자 동적 그룹 강화
기본적으로 **모든 사용자** 그룹에는 B2B 공동 작업(게스트) 사용자도 포함됩니다. 게스트 사용자를 제거하는 규칙을 사용하여 **모든 사용자** 그룹을 추가로 보호할 수 있습니다. 다음 그림은 게스트를 제외하도록 수정된 **모든 사용자** 그룹을 보여 줍니다.

![사용자 형식이 게스트와 같지 않은 규칙 표시](media/use-dynamic-groups/exclude-guest-users.png)

정책(예: Azure AD 조건부 액세스 정책)을 적용할 수 있도록 게스트 사용자만 포함된 새 동적 그룹을 만드는 것이 유용할 수도 있습니다.
이러한 그룹은 다음과 같이 표시됩니다.

![사용자 형식이 게스트와 같은 규칙 표시](media/use-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>다음 단계

- [B2B 공동 작업 사용자 속성](user-properties.md)
- [역할에 B2B 공동 작업 사용자 추가](add-guest-to-role.md)
- [B2B 공동 작업 사용자에 대한 조건부 액세스](conditional-access.md)

