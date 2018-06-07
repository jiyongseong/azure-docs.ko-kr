---
title: 앱 인증에 대한 값 가져오기 - Azure SQL Database | Microsoft Docs
description: 코드에서 SQL Database에 액세스하기 위한 서비스 사용자를 만듭니다.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: f939f059ce2777eb32c869d8a18994da1f9873de
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>코드에서 SQL Database에 액세스하는 응용 프로그램을 인증하기 위한 필수 값 가져오기
코드에서 SQL Database를 만들고 관리하여 Azure 리소스가 생성된 구독의 Azure Active Directory(AAD) 도메인에 앱을 등록해야 합니다.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>응용 프로그램에서 리소스에 액세스하는 서비스 주체 만들기
최신 [Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx)을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.

다음 PowerShell 스크립트는 AD(Active Directory) 응용 프로그램 및 C# 앱을 인증해야 하는 서비스 주체를 만듭니다. 스크립트는 이전 C# 샘플에 필요한 값을 출력합니다. 자세한 내용은 [Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](../azure-resource-manager/resource-group-authenticate-service-principal.md)를 참조하세요.

    # Sign in to Azure.
    Connect-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>참고 항목
* [C#으로 SQL 데이터베이스 만들기](sql-database-get-started-csharp.md)
* [Azure Active Directory 인증을 사용하여 SQL Database에 연결](sql-database-aad-authentication.md)

