---
title: Azure Active Directory의 Office 365 그룹 만료 | Microsoft Docs
description: Azure Active Directory에서 Office 365 그룹에 대한 만료를 설정하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 03/09/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 1e500be80374edd4d2f53fd2f0604e4a63f640fc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Office 365 그룹에 대한 만료 정책 구성

이제 Office 365 그룹에 대한 만료 정책을 설정하여 Office 365 그룹의 수명 주기를 관리할 수 있습니다. Azure AD(Azure Active Directory)에서는 Office 365 그룹에 대해서만 만료 정책을 설정할 수 있습니다. 

만료할 그룹을 설정한 경우 다음이 적용됩니다.
-   만료가 임박하면 그룹의 소유자에게 그룹을 갱신하라는 알림이 표시됩니다.
-   갱신하지 않는 모든 그룹은 삭제됩니다.
-   삭제된 Office 365 그룹은 그룹 소유자 또는 관리자에 의해 30일 이내로 복원될 수 있습니다.

> [!NOTE]
> Office 365 그룹에 대해 만료 정책을 구성 및 사용하려면 만료 정책이 적용되는 그룹의 모든 멤버에 대해 Azure AD Premium 라이선스가 있어야 합니다.

Azure AD PowerShell cmdlet을 다운로드하여 설치하는 방법에 대한 내용은 [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)을 참조하세요.

## <a name="roles-and-permissions"></a>역할 및 권한
다음은 Azure AD에서 Office 365 그룹의 만료를 구성하고 사용할 수 있는 역할입니다.

역할 | 권한
-------- | --------
전역 관리자 또는 사용자 계정 관리자 | Office 365 그룹 만료 정책 설정을 만들거나, 읽거나, 업데이트하거나, 삭제할 수 있음<br>Office 365 그룹을 갱신할 수 있음
사용자 | 소유하는 Office 365 그룹을 갱신할 수 있음<br>소유하는 Office 365 그룹을 복원할 수 있음<br>만료 정책 설정을 읽을 수 있음

삭제된 그룹을 복원하는 권한에 대한 자세한 내용은 [Azure Active Directory에서 삭제된 Office 365 그룹 복원](active-directory-groups-restore-azure-portal.md)을 참조하세요.

## <a name="set-group-expiration"></a>그룹 만료 설정

1. Azure AD 테넌트에서 전역 관리자인 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)를 엽니다.

2. **그룹**을 선택한 다음 **만료**를 선택하여 만료 설정을 엽니다.
  
  ![만료 블레이드](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. **만료** 블레이드에서 다음을 수행할 수 있습니다.

  * 일 단위로 그룹 수명을 설정합니다. 미리 설정된 값 중 하나 또는 사용자 지정 값을 선택할 수 있습니다(31일 이상이어야 함). 
  * 그룹에 소유자가 없는 경우 갱신 및 만료 알림이 전송되어야 하는 전자 메일 주소를 지정합니다. 
  * 만료되는 Office 365 그룹을 선택합니다. **모든** Office 365 그룹에 대한 만료를 설정하거나, **선택된** Office 365 그룹만 사용하도록 선택하거나 **없음**을 선택하여 모든 그룹에 대해 만료를 비활성화하도록 선택할 수 있습니다.
  * 완료되면 **저장**을 선택하여 설정을 저장합니다.


이와 같은 전자 메일 알림은 그룹의 만료 30일, 15일 및 1일 전에 Office 365 그룹 소유자에게 전송됩니다.

![만료 전자 메일 알림](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

**그룹 갱신** 알림 이메일에서 그룹 소유자는 액세스 패널의 그룹 세부 정보 페이지에 직접 액세스할 수 있습니다. 여기에 사용자는 그룹에 대해 마지막 갱신 시간, 예상 만료 시간, 그룹 갱신 능력 등에 대한 자세한 정보를 얻을 수 있습니다. 이제 그룹 세부 정보 페이지에는 Office 365 그룹 리소스에 대한 링크가 포함되어 있으므로 그룹 소유자는 해당 그룹의 콘텐츠 및 활동을 편리하게 볼 수 있습니다.

그룹이 만료되면 그룹은 만료 날짜 1일 후에 삭제됩니다. Office 365 그룹의 만료 및 후속 삭제에 대해 알려주는 이와 같은 전자 메일 알림은 Office 365 그룹 소유자에게 전송됩니다.

![그룹 삭제 전자 메일 알림](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

[Azure Active Directory에서 삭제된 Office 365 그룹 복원](active-directory-groups-restore-azure-portal.md)에서 설명된 대로 **그룹 복원**을 선택하거나 PowerShell cmdlet을 사용하여 30일 내에 그룹을 복원할 수 있습니다.
    
복원하는 그룹에 문서, SharePoint 사이트 또는 기타 영구 개체가 포함된 경우 그룹 및 해당 내용을 완전히 복원하는 데 최대 24시간이 걸릴 수 있습니다.

> [!NOTE]
> * 만료를 처음 설정할 경우 만료 간격보다 오래된 모든 그룹은 만료되기 전까지 30일로 설정됩니다. 하루 이내로 첫 번째 갱신 알림 전자 메일이 전송됩니다. 
>   예를 들어 그룹 A가 400일 전에 만들어졌으며 만료 기간은 180일로 설정됩니다. 만료 설정을 적용하면 그룹 A는 소유자가 갱신하지 않는 한 삭제되기 전에 30일이 있습니다.
> * 현재, 테넌트의 Office 365 그룹에 대해 만료 정책을 하나만 구성할 수 있습니다.
> * 동적 그룹이 삭제되고 복원되는 경우 새 그룹으로 표시되며 규칙에 따라 다시 채워집니다. 이 프로세스는 최대 24시간까지 걸릴 수 있습니다.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>법적 보존 상태의 사서함에 Office 365 그룹 만료가 작동하는 방식
그룹에 만료되고 삭제되면 삭제되고 30일 후에 Planner, Sites 또는 Teams 같은 앱의 그룹 데이터가 영구히 삭제되지만, 법적 보존 상태인 그룹 사서함은 영구적으로 삭제되지 않습니다. 관리자는 Exchange cmdlet을 사용하여 사서함을 복원한 후 데이터를 인출합니다. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>보존 정책에 Office 365 그룹 만료가 작동하는 방식
보존 정책은 보안 및 준수 센터를 통해 구성됩니다. Office 365 그룹에 대해 보존 정책을 설정한 경우, 그룹이 만료되고 삭제되면 그룹 사서함의 그룹 대화와 그룹 사이트의 파일은 보존 기간에 정의된 특정 기간(일) 동안 보존 컨테이너에 유지됩니다. 사용자는 만료된 후 그룹 또는 해당 콘텐츠를 볼 수 없지만, eDiscovery를 통해 사이트 및 사서함 데이터를 복구할 수 있습니다.

## <a name="powershell-examples"></a>PowerShell 예제
PowerShell cmdlet을 사용하여 테넌트의 Office 365 그룹에 대해 만료 설정을 구성하는 방법의 예는 다음과 같습니다.

1. PowerShell v 2.0 Preview 모듈(2.0.0.137)를 설치하고 PowerShell 프롬프트에 로그인합니다.
  ````
  Install-Module -Name AzureADPreview
  connect-azuread 
  ````
2. 만료 설정 구성 New-AzureADMSGroupLifecyclePolicy: 이 cmdlet은 테넌트의 모든 Office 365 그룹에 대한 수명을 365일로 설정합니다. 소유자 없는 Office 365 그룹에 대한 갱신 알림은 'emailaddress@contoso.com'으로 전송됩니다.
  
  ````
  New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
  ````
3. 기존 정책 검색: Get-AzureADMSGroupLifecyclePolicy: 이 cmdlet은 구성된 현재 Office 365 그룹 만료 설정을 검색합니다. 이 예제에서는 다음을 확인할 수 있습니다.
  * 정책 ID 
  * 테넌트의 모든 Office 365 그룹에 대한 수명이 365일로 설정됨
  * 소유자 없는 Office 365 그룹에 대한 갱신 알림은 'emailaddress@contoso.com'으로 전송됩니다.
  
  ````
  Get-AzureADMSGroupLifecyclePolicy
  
  ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
  --                                    ------------------- ----------------- ---------------------------
  26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
  ```` 
   
4. 기존 정책 업데이트 Set-AzureADMSGroupLifecyclePolicy: 이 cmdlet은 기존 정책을 업데이트하는 데 사용됩니다. 아래 예제에서 기존 정책의 그룹 수명은 365일에서 180일로 변경됩니다. 
  
  ````
  Set-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”   -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
  ````
  
5. 정책에 특정 그룹 추가 Add-AzureADMSLifecyclePolicyGroup: 이 cmdlet은 수명 주기 정책에 그룹을 추가합니다. 예를 들어 
  
  ````
  Add-AzureADMSLifecyclePolicyGroup -Id “26fcc232-d1c3-4375-b68d-15c296f1f077” -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
  ````
  
6. 기존 정책 제거 Remove-AzureADMSGroupLifecyclePolicy: 이 cmdlet은 Office 365 그룹 만료 설정을 삭제하지만 정책 ID가 필요합니다 Office 365 그룹에 대한 만료가 사용되지 않도록 설정됩니다. 
  
  ````
  Remove-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”
  ````
  
다음 cmdlet은 정책을 보다 자세히 구성하는 데 사용할 수 있습니다. 자세한 내용은 [PowerShell 설명서](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups)를 참조하세요.

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>다음 단계
이러한 문서는 Azure AD 그룹에 대한 추가 정보를 제공합니다.

* [기존 그룹 보기](active-directory-groups-view-azure-portal.md)
* [그룹의 설정 관리](active-directory-groups-settings-azure-portal.md)
* [그룹의 멤버 관리](active-directory-groups-members-azure-portal.md)
* [그룹의 멤버 자격 관리](active-directory-groups-membership-azure-portal.md)
* [그룹의 사용자에 대한 동적 규칙 관리](active-directory-groups-dynamic-membership-azure-portal.md)
