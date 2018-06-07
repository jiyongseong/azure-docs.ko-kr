---
title: Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당 | Microsoft Docs
description: Azure Active Directory에서 사용자 또는 그룹을 할당할 엔터프라이즈 앱을 선택하는 방법
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
ms.date: 04/19/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: 1609942269a0c737239cf4198289fdc44ade2b32
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당
엔터프라이즈 앱에 사용자 또는 그룹을 할당하려면 엔터프라이즈 앱을 관리하기 위한 적절한 권한이 있어야 하고 해당 디렉터리에 대한 전역 관리자여야 합니다.
> [!NOTE]
> Microsoft 응용 프로그램(예: Office 365 앱)의 경우 PowerShell을 사용하여 엔터프라이즈 앱에 사용자를 할당합니다.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Azure Portal에서 엔터프라이즈 앱에 사용자 액세스를 할당하려면 어떻게 하나요?
1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스**를 선택하고 텍스트 상자에 Azure Active Directory를 입력한 다음, **입력**을 선택합니다.
3. **Azure Active Directory - *directoryname*** 블레이드(즉, 관리 중인 디렉터리에 대한 Azure AD 블레이드)에서 **엔터프라이즈 응용 프로그램**을 선택합니다.

    ![엔터프라이즈 앱 열기](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. **엔터프라이즈 응용 프로그램** 블레이드에서 **모든 응용 프로그램**을 선택합니다. 그러면 관리할 수 있는 앱이 표시됩니다.
5. **엔터프라이즈 응용 프로그램 - 모든 응용 프로그램** 블레이드에서 앱을 선택합니다.
6. ***appname*** 블레이드(즉, 제목에서 선택된 앱의 이름을 가진 블레이드)에서 **사용자 및 그룹**을 선택합니다.

    ![모든 응용 프로그램 명령 선택](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. ***appname*** **- 사용자 및 그룹 할당** 블레이드에서 **추가** 명령을 선택합니다.
8. **할당 추가** 블레이드에서 **사용자 및 그룹**을 선택합니다.

    ![앱에 사용자 또는 그룹 할당](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. **사용자 및 그룹** 블레이드의 목록에서 하나 이상의 사용자 또는 그룹을 선택한 다음 블레이드 맨 아래의 **선택** 단추를 선택합니다.
10. **할당 추가** 블레이드에서 **역할**을 선택합니다. 그런 다음 **역할 선택** 블레이드에서 선택한 사용자 또는 그룹에 적용할 역할을 선택한 다음 블레이드 맨 아래의 **확인** 단추를 선택합니다.
11. **할당 추가** 블레이드에서 블레이드 맨 아래의 **할당** 단추를 선택합니다. 할당된 사용자 또는 그룹은 이 엔터프라이즈 앱에 대한 선택된 역할로 정의된 사용 권한을 갖습니다.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>PowerShell을 사용하여 엔터프라이즈 앱에 사용자를 할당하려면 어떻게 하나요?

1. 관리자 권한 Windows PowerShell 명령 프롬프트를 엽니다.

    >[!NOTE] 
    > Azure AD 모듈을 설치해야 합니다(`Install-Module -Name AzureAD` 명령 사용). NuGet 모듈 또는 새로운 Azure Active Directory V2 PowerShell 모듈을 설치하라는 메시지가 표시되면 Y를 입력하고 Enter 키를 누릅니다.

2. `Connect-AzureAD`를 실행하고 전역 관리자 사용자 계정으로 로그인합니다.
3. 다음 스크립트를 사용하여 응용 프로그램에 사용자 및 역할을 할당합니다.

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

응용 프로그램 역할에 사용자를 할당하는 방법에 대한 자세한 내용은 [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)에 대한 설명서를 참조하세요.

그룹을 엔터프라이즈 앱에 할당하려면 `Get-AzureADUser`를 `Get-AzureADGroup`으로 바꿔야 합니다.

### <a name="example"></a>예

이 예제에서는 PowerShell을 사용하여 사용자 Britta Simon을 [Microsoft Workplace Analytics](https://products.office.com/en-us/business/workplace-analytics) 응용 프로그램에 할당합니다.

1. PowerShell에서 변수 $username, app_name $ 및 $app_role_name에 해당 값을 할당합니다. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. 이 예제에서는 Britta Simon에 할당하려는 응용 프로그램 역할의 정확한 이름을 모릅니다. 다음 명령을 실행하여 사용자 UPN 및 서비스 주체 표시 이름으로 사용자($user) 및 서비스 주체($sp)를 가져옵니다.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. 명령 `$sp.AppRoles`를 실행하여 Workplace Analytics 응용 프로그램에 대해 사용할 수 있는 역할을 표시합니다. 이 예제에서는 Britta Simon을 Analyst(제한된 액세스) 역할에 할당하려고 합니다.
    
    ![Workplace Analytics 역할](media/active-directory-coreapps-assign-user-azure-portal/workplace-analytics-role.png)

4. 역할 이름을 `$app_role_name` 변수에 할당합니다.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. 다음 명령을 실행하여 앱 역할에 사용자를 할당합니다.

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>다음 단계
* [내 그룹 모두 보기](active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](active-directory-coreapps-remove-assignment-azure-portal.md)
* [엔터프라이즈 앱에 대한 사용자 로그인 비활성화](active-directory-coreapps-disable-app-azure-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](active-directory-coreapps-change-app-logo-user-azure-portal.md)
