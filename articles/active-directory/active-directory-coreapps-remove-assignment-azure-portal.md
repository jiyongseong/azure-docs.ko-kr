---
title: Azure Active Directory에서 엔터프라이즈 앱의 사용자 또는 그룹 할당 제거 | Microsoft Docs
description: Azure Active Directory에서 엔터프라이즈 앱의 사용자 또는 그룹의 액세스 할당을 제거하는 방법
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: b3c5c6c4bc1cfd5a9b68277e21fc06f37fa18f3b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱의 사용자 또는 그룹 할당 제거
Azure AD(Azure Active Directory)에서 엔터프라이즈 응용 프로그램 중 하나에 대해 할당된 액세스의 사용자 또는 그룹을 제거하는 것은 쉽습니다. 엔터프라이즈 앱을 관리하려면 적절한 권한이 있어야 하고 해당 디렉터리에 대한 전역 관리자여야 합니다.

> [!NOTE]
> Microsoft 응용 프로그램(예: Office 365 앱)의 경우 PowerShell을 사용하여 엔터프라이즈 앱에 대한 사용자를 제거합니다.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Azure Portal에서 엔터프라이즈 앱에 대한 사용자 또는 그룹 할당을 어떻게 제거하나요?
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **더 많은 서비스**를 선택하고 텍스트 상자에서 **Azure Active Directory**를 입력한 다음 **Enter**를 선택합니다.
3. **Azure Active Directory - directoryname** 페이지, 즉 관리 중인 디렉터리에 대한 Azure AD 페이지에서 **엔터프라이즈 응용 프로그램**을 선택합니다.

    ![엔터프라이즈 앱 열기](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. **엔터프라이즈 응용 프로그램** 페이지에서 **모든 응용 프로그램**을 선택합니다. 관리할 수 있는 앱의 목록이 표시됩니다.
5. **엔터프라이즈 응용 프로그램 - 모든 응용 프로그램** 페이지에서 앱을 선택합니다.
6. ***appname*** 페이지(즉, 제목에서 선택된 앱의 이름을 가진 페이지)에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 또는 그룹 선택](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. ***appname*** **- 사용자 및 그룹 할당** 페이지에서 하나 이상의 사용자 또는 그룹을 선택한 다음, **제거** 명령을 선택합니다. 프롬프트에서 의사 결정을 확인합니다.

    ![제거 명령 선택](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>PowerShell을 사용하여 엔터프라이즈 앱에 대한 사용자 또는 그룹 할당을 어떻게 제거하나요?
1. 관리자 권한 Windows PowerShell 명령 프롬프트를 엽니다.

    >[!NOTE] 
    > Azure AD 모듈을 설치해야 합니다(`Install-Module -Name AzureAD` 명령 사용). NuGet 모듈 또는 새로운 Azure Active Directory V2 PowerShell 모듈을 설치하라는 메시지가 표시되면 Y를 입력하고 Enter 키를 누릅니다.

2. `Connect-AzureAD`를 실행하고 전역 관리자 사용자 계정으로 로그인합니다.
3. 다음 스크립트를 사용하여 응용 프로그램에 사용자 및 역할을 할당합니다.

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ``` 
## <a name="next-steps"></a>다음 단계

- [내 그룹 모두 보기](active-directory-groups-view-azure-portal.md)
- [엔터프라이즈 앱에 사용자 또는 그룹 할당](active-directory-coreapps-assign-user-azure-portal.md)
- [엔터프라이즈 앱에 대한 사용자 로그인 비활성화](active-directory-coreapps-disable-app-azure-portal.md)
- [엔터프라이즈 앱의 이름 또는 로고 변경](active-directory-coreapps-change-app-logo-user-azure-portal.md)
