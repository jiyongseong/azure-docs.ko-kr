---
title: Azure Active Directory에서 삭제된 Office 365 그룹 복원 | Microsoft Docs
description: Azure Active Directory에서 삭제된 그룹을 복원하고 복원 가능한 그룹을 보고 영구적으로 그룹을 삭제하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 388c5617a040da396cb0c6a05b5697fb5fd22248
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="restore-a-deleted-office-365-group-in-azure-active-directory"></a>Azure Active Directory에서 삭제된 Office 365 그룹 복원

Azure AD(Azure Active Directory)에서 Office 365 그룹을 삭제하는 경우 삭제된 그룹은 유지되지만 삭제일로부터 30일 동안 표시되지 않습니다. 필요한 경우 그룹 및 해당 콘텐츠를 복원할 수 있습니다. 이 기능은 Azure AD의 Office 365 그룹에만 제한됩니다. 보안 그룹 및 배포 그룹에는 사용할 수 없습니다.

> [!NOTE] 
> `Remove-MsolGroup`을 사용하면 그룹이 영구적으로 제거되므로 사용하지 않도록 합니다. 항상 `Remove-AzureADMSGroup`을 사용하여 O365 그룹을 삭제하세요. 

그룹을 복원하는 데 필요한 사용 권한은 다음 중 하나일 수 있습니다.

역할  | 권한 
--------- | ---------
회사 관리자, 파트너 계층2 지원 및 InTune 서비스 관리자 | 모든 삭제된 Office 365 그룹을 복원할 수 있음 
사용자 계정 관리자 및 파트너 계층1 지원 | 회사 관리자 역할에 할당된 것을 제외한 모든 삭제된 Office 365 그룹을 복원할 수 있음 
사용자 | 소유했던 모든 삭제된 Office 365 그룹을 복원할 수 있음 


## <a name="view-the-deleted-office-365-groups-that-are-available-to-restore"></a>복원할 수 있는 삭제된 Office 365 그룹 보기
다음 cmdlet을 사용하여 관심 있는 그룹이 영구적으로 제거되었는지 확인하기 위해 삭제된 그룹을 볼 수 있습니다. 이러한 cmdlet은 [Azure AD PowerShell 모듈](https://www.powershellgallery.com/packages/AzureAD/)에 포함됩니다. 이 모듈에 대한 자세한 정보는 [Azure Active Directory PowerShell 버전 2](/powershell/azure/install-adv2?view=azureadps-2.0) 문서에서 찾을 수 있습니다.

1.  다음 cmdlet을 실행하여 여전히 복원할 수 있는 테넌트의 모든 삭제된 Office 365 그룹을 표시합니다.
  ```
  Get-AzureADMSDeletedGroup
  ```

2.  또는 특정 그룹의 objectID를 아는 경우(및 1단계의 cmdlet에서 가져올 수 있는 경우) 다음 cmdlet을 실행하여 삭제된 특정 그룹이 영구적으로 제거되었는지 확인합니다.
  ```
  Get-AzureADMSDeletedGroup –Id <objectId>
  ```



## <a name="how-to-restore-your-deleted-office-365-group"></a>삭제된 Office 365 그룹을 복원하는 방법
그룹을 복원할 수 있는지 확인한 후 다음 단계 중 하나를 사용하여 삭제된 그룹을 복원합니다. 그룹에 문서, SP 사이트 또는 기타 영구 개체가 포함된 경우 그룹 및 해당 내용을 완전히 복원하는 데 최대 24시간이 걸릴 수 있습니다.

1.  다음 cmdlet을 실행하여 그룹 및 해당 내용을 복원합니다.
  
  ```
  Restore-AzureADMSDeletedDirectoryObject –Id <objectId>
  ``` 

또는 다음 cmdlet을 실행하여 삭제된 그룹을 영구적으로 제거할 수 있습니다.
  ```
  Remove-AzureADMSDeletedDirectoryObject –Id <objectId>
  ```

## <a name="how-do-you-know-this-worked"></a>작동되었는지 어떻게 알 수 있습니까?
Office 365 그룹을 성공적으로 복원했는지 확인하려면 `Get-AzureADGroup –ObjectId <objectId>` cmdlet을 실행하여 그룹에 대한 정보를 표시합니다. 복원 후 요청이 완료됩니다.
- 그룹은 Exchange의 왼쪽 탐색 모음에 표시됩니다.
- 그룹에 대한 계획은 Planner에 표시됩니다.
- 모든 Sharepoint 사이트와 모든 해당 콘텐츠를 사용할 수 있습니다.
- Office 365 그룹을 지원하는 Exchange 끝점 및 다른 Office 365 워크로드 중 하나에서 그룹에 액세스할 수 있습니다.


## <a name="next-steps"></a>다음 단계
이러한 문서는 Azure Active Directory 그룹에 대한 추가 정보를 제공합니다.

* [기존 그룹 보기](active-directory-groups-view-azure-portal.md)
* [그룹의 설정 관리](active-directory-groups-settings-azure-portal.md)
* [그룹의 멤버 관리](active-directory-groups-members-azure-portal.md)
* [그룹의 멤버 자격 관리](active-directory-groups-membership-azure-portal.md)
* [그룹의 사용자에 대한 동적 규칙 관리](active-directory-groups-dynamic-membership-azure-portal.md)
