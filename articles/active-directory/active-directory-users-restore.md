---
title: Azure Active Directory에는 최근에 삭제된 사용자 복원 또는 영구히 제거 | Microsoft Docs
description: Azure Active Directory에서 삭제된 사용자를 복원하거나, 복원 가능한 사용자를 보거나, 영구적으로 사용자를 삭제하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 05/09/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: 8dbb546954f8eee0bf997b3d2f4f92d67c0b7869
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930877"
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Azure Active Directory에서 삭제된 사용자 복원

이 문서에는 이전에 삭제된 사용자를 복원하거나 영구적으로 삭제하는 지침이 포함되어 있습니다. Azure AD(Azure Active Directory)에서 사용자를 삭제하는 경우 삭제된 사용자가 삭제 날짜부터 30일 동안 유지됩니다. 이 시간 중에는 사용자와 해당 속성을 복원할 수 있습니다. 

> [!wARNING]
> 사용자가 영구적으로 삭제된 후에는 복원할 수 없습니다.


## <a name="how-to-restore-a-recently-deleted-user"></a>최근에 삭제된 사용자를 복원하는 방법
사용자가 최근에 삭제된 경우 모든 디렉터리 정보가 유지됩니다. 사용자가 복원되면 해당 정보도 복원됩니다.

1. [Azure AD 관리 센터](https://aad.portal.azure.com)에서 **사용자** &gt; **삭제된 사용자**를 선택합니다. 
2. 최근에 삭제된 사용자를 하나 이상 선택합니다.
3. **사용자 복원**을 선택합니다.

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>최근에 삭제된 사용자를 영구 삭제하는 방법

1. [Azure AD 관리 센터](https://aad.portal.azure.com)에서 **사용자** &gt; **삭제된 사용자**를 선택합니다. 
2. 최근에 삭제된 사용자를 하나 이상 선택합니다.
3. **영구적으로 삭제**를 선택합니다.

## <a name="required-permissions"></a>필요한 사용 권한
다음 권한이 있으면 사용자를 복원할 수 있습니다.

역할  | 권한 
--------- | ---------
회사 관리자<p>파트너 계층1 지원<p>파트너 계층2 지원<p>사용자 계정 관리자 | 삭제된 사용자를 복원할 수 있음 
회사 관리자<p>파트너 계층1 지원<p>파트너 계층2 지원<p>사용자 계정 관리자 | 사용자를 영구적으로 삭제할 수 있음

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Active Directory 사용자 관리에 대한 추가 정보를 제공합니다.

* [빠른 시작: Azure Active Directory에서 사용자 추가 또는 삭제](add-users-azure-active-directory.md)
* [사용자 프로필 관리](active-directory-users-profile-azure-portal.md)
* [다른 디렉터리에서 게스트 사용자 추가](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Azure AD의 역할에 사용자 할당](active-directory-users-assign-role-azure-portal.md)
