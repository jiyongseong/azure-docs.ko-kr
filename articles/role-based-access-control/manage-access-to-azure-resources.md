---
title: Azure Active Directory를 사용하여 Azure 리소스에 대한 액세스 관리
description: Azure Active Directory의 다양한 기능을 사용하여 Azure 리소스에 대한 액세스를 관리하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: f66abf54-3809-436c-92b6-018e1179780e
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/05/2017
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 55bd18b127fea8a06e58f14873a584f0b5849906
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="manage-access-to-azure-resources-with-azure-active-directory"></a>Azure Active Directory를 사용하여 Azure 리소스에 대한 액세스 관리

클라우드 리소스에 대한 ID 및 액세스 관리는 클라우드를 사용하는 모든 조직에서 중요한 기능입니다. Azure AD(Azure Active Directory)는 Microsoft Azure의 ID 및 액세스 시스템입니다.  

지원되는 Azure AD의 기능 영역을 탐색하기 전에 다음 비디오를 확인하세요. "SSO, 역할 기반 액세스 제어 및 조건부를 사용하여 Azure 클라우드에 대한 액세스 잠금" 여기에서 다음에 대해 알아봅니다.

- 온-프레미스 Active Directory를 사용하여 Azure Portal에 대한 Single Sign-On을 구성하는 모범 사례
- 구독에서 리소스에 대한 세분화된 액세스 제어를 위해 Azure RBAC 사용
- Azure AD 조건부 액세스를 사용하여 강력한 인증 규칙 적용
- 관리 서비스 ID의 개념에서 Azure 리소스는 자동으로 Azure 서비스에 인증할 수 있고 개발자는 API 키 또는 비밀을 처리할 필요가 없습니다.

> [!VIDEO https://www.youtube.com/embed/FKBoWWKRnvI]

## <a name="feature-areas"></a>기능 영역
Azure AD는 Azure 리소스에 대한 액세스를 관리하기 위한 다음과 같은 기능을 제공합니다.

|||
|---|---|
| [Azure AD 테넌트와 구독 사이의 관계](rbac-and-directory-admin-roles.md) | Azure AD가 어떻게 Azure 구독에 대한 사용자 및 그룹의 신뢰할 수 있는 소스인지 자세히 알아보세요. |
| [RBAC(역할 기반 액세스 제어)](overview.md) | 사용자, 그룹 또는 서비스 주체에 할당된 역할을 통해 세분화된 액세스 관리를 제공합니다. |
| [RBAC로 Privileged Identity Management](pim-azure-resource.md) | 권한 있는 역할을 Just-In-Time에 할당하여 높은 권한의 액세스를 제어합니다. |
| [Azure 관리에 대한 조건부 액세스](conditional-access-azure-management.md) | Azure 관리 끝점에 대한 액세스를 허용 또는 차단하도록 조건부 액세스 정책을 설정합니다. |
| [MSI(관리되는 서비스 ID)](../active-directory/pp/msi-overview.md) | Virtual Machines와 같은 Azure 리소스에 사용자 고유의 ID를 제공하므로 코드에 자격 증명을 넣지 않아도 됩니다. |

 