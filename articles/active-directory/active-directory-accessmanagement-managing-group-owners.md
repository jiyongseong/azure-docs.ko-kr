---
title: 그룹을 사용하는 액세스 관리의 다음 단계 | Microsoft Docs
description: 보안 그룹 관리에 대한 고급 방법과 이러한 그룹을 사용하여 리소스에 대한 액세스를 관리하는 방법입니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/12/2017
ms.author: curtand
ms.custom: it-pro
ms.openlocfilehash: e4703ab5d9f4b8593e758764e50455672e368e18
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="managing-owners-for-a-group"></a>그룹에 대한 소유자 관리
리소스 소유자가 Azure AD 그룹에 리소스에 대한 액세스 권한을 할당하면 그룹의 멤버 자격은 그룹 소유자에 의해 관리됩니다. 실질적으로 리소스 소유자는 사용자를 해당 리소스에 할당할 권한을 그룹 소유자에게 위임합니다.

## <a name="add-an-owner-to-a-group"></a>그룹에 소유자 추가

1. [Azure AD 관리 센터](https://aad.portal.azure.com)에서 **사용자 및 그룹**을 선택합니다.
2. **모든 그룹**을 선택하고 소유자를 추가할 그룹을 엽니다.
3. **소유자 추가**를 선택합니다.
4. **소유자 추가** 페이지에서 이 그룹의 소유자로 추가할 사용자를 선택하고 이 이름이 **선택 항목** 창에 추가되었는지 확인합니다.

## <a name="remove-an-owner-from-a-group"></a>그룹에서 소유자 제거

1. [Azure AD 관리 센터](https://aad.portal.azure.com)에서 **사용자 및 그룹**을 선택합니다.
2. **모든 그룹**을 선택하고 소유자를 제거할 그룹을 엽니다.
3. **소유자** 탭을 선택합니다.
4. 이 그룹에서 제거할 소유자를 선택한 후 **제거**를 선택합니다.

## <a name="additional-information"></a>추가 정보
이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](active-directory-manage-groups.md)
* [그룹 설정을 구성하는 Azure Active Directory cmdlet](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory란?](active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
