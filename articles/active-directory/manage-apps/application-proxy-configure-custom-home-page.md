---
title: Azure AD 응용 프로그램 프록시를 사용하여 게시된 앱에 대해 사용자 지정 홈 페이지 설정 | Microsoft Docs
description: Azure AD 응용 프로그램 프록시 커넥터에 대한 기본 사항 제공
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a1801af582e9e4bfa82dab4b5916c164fcf3bb76
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161566"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용하여 게시된 앱에 대해 사용자 지정 홈 페이지 설정

이 문서에서는 사용자를 사용자 지정 홈 페이지에 연결하도록 앱을 구성하는 방법을 설명합니다. 응용 프로그램 프록시를 사용하여 응용 프로그램을 게시할 때 내부 URL을 설정하지만 사용자가 처음 보게 되는 페이지는 설정하지 않는 경우가 있습니다. 사용자가 앱에 액세스할 때 올바른 페이지로 이동하도록 사용자 지정 홈 페이지를 설정합니다. Azure Active Directory 액세스 패널 또는 Office 365 앱 시작 관리자에서 앱에 액세스할 때 여기서 설정한 사용자 지정 홈 페이지로 이동하게 됩니다.

사용자가 앱을 시작하면 기본적으로 게시된 앱의 루트 도메인 URL로 이동됩니다. 방문 페이지는 일반적으로 홈페이지 URL로 설정됩니다. 앱 사용자가 앱 내의 특정 페이지를 방문하게 하려는 경우 Azure AD PowerShell 모듈을 사용하여 사용자 지정 홈 페이지 URL을 정의합니다. 

회사가 사용자 지정 홈 페이지를 설정하는 이유의 한 예는 다음과 같습니다.
- 사용자가 회사 네트워크 내에서 *https://ExpenseApp/login/login.aspx*로 이동하여 로그인하고 앱에 액세스합니다.
- 응용 프로그램 프록시가 폴더 구조의 최상위 수준에서 액세스해야 하는 이미지와 같은 다른 자산이 있으므로 *https://ExpenseApp*을 내부 URL로 사용하여 앱을 게시합니다.
- 기본 외부 URL은 *https://ExpenseApp-contoso.msappproxy.net*이며, 사용자를 로그인 페이지로 이동하지 않습니다.  
- *https://ExpenseApp-contoso.msappproxy.net/login/login.aspx*를 홈페이지 URL로 설정합니다. 

>[!NOTE]
>사용자에게 게시된 응용 프로그램에 대한 액세스 권한을 제공하면 [Azure AD 액세스 패널](../active-directory-saas-access-panel-introduction.md) 및 [Office 365 앱 시작 관리자](https://blogs.office.com/2016/09/27/introducing-the-new-office-365-app-launcher)에 앱이 표시됩니다.

## <a name="before-you-start"></a>시작하기 전에

홈페이지 URL을 설정하기 전에 다음 요구 사항에 유의하세요.

* 사용자가 지정한 경로가 루트 도메인 URL의 하위 도메인 경로인지 확인합니다.

  예를 들어 루트 도메인 URL이 https://apps.contoso.com/app1/이면 구성하는 홈페이지 URL은 https://apps.contoso.com/app1/로 시작해야 합니다.

* 게시된 앱을 변경하는 경우 이로 인해 홈페이지 URL 값을 다시 설정할 수 있습니다. 나중에 앱을 업데이트할 경우 홈페이지 URL을 다시 확인하고 필요한 경우 업데이트해야 합니다.

## <a name="change-the-home-page-in-the-azure-portal"></a>Azure Portal에서 홈페이지 변경

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **앱 등록**으로 이동한 후 목록에서 응용 프로그램을 선택합니다. 
3. 설정에서 **속성**을 선택합니다.
4. **홈페이지 URL** 필드를 새 경로로 업데이트합니다. 

   ![새 홈페이지 URL 제공](./media/application-proxy-configure-custom-home-page/homepage.png)

5. **저장**을 선택합니다.

## <a name="change-the-home-page-with-powershell"></a>PowerShell을 사용하여 홈페이지 변경

### <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell 모듈 설치

PowerShell을 사용하여 사용자 지정 홈페이지 URL을 정의하기 전에 Azure AD PowerShell 모듈을 설치합니다. Graph API 끝점을 사용하는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAD/2.0.0.131)에서 패키지를 다운로드할 수 있습니다. 

패키지를 설치하려면 다음 단계를 따르세요.

1. 표준 PowerShell 창을 열고 다음 명령을 실행합니다.

    ```
     Install-Module -Name AzureAD
    ```
    관리자가 아닌 사용자로 명령을 실행하는 경우 `-scope currentuser` 옵션을 사용합니다.
2. 설치 도중 Nuget.org에서 두 개의 패키지를 설치하려면 **Y**를 선택합니다. 두 개의 패키지가 모두 필요합니다. 

### <a name="find-the-objectid-of-the-app"></a>앱의 ObjectID 찾기

앱의 ObjectID를 받은 다음 해당 홈 페이지에서 앱을 검색합니다.

1. 동일한 PowerShell 창에서 Azure AD 모듈을 가져옵니다.

    ```
    Import-Module AzureAD
    ```

2. 테넌트 관리자로 Azure AD 모듈에 로그인합니다.

    ```
    Connect-AzureAD
    ```
3. 홈페이지 URL을 기준으로 앱을 찾습니다. **Azure Active Directory** > **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**으로 이동하여 포털에서 URL을 찾을 수 있습니다. 이 예제에서는 *sharepoint-iddemo*를 사용합니다.

    ```
    Get-AzureADApplication | where { $_.Homepage -like “sharepoint-iddemo” } | fl DisplayName, Homepage, ObjectID
    ```
4. 여기에 표시된 것과 비슷한 결과가 표시되어야 합니다. 다음 섹션에서 사용할 ObjectID GUID를 복사합니다.

    ```
    DisplayName : SharePoint
    Homepage    : https://sharepoint-iddemo.msappproxy.net/
    ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

### <a name="update-the-home-page-url"></a>홈페이지 URL 업데이트

홈 페이지 URL을 만들고 해당 값으로 응용 프로그램을 업데이트합니다. 동일한 PowerShell 창에서 해당 명령을 실행합니다. 또는 새 PowerShell 창을 사용할 경우 다시 `Connect-AzureAD`를 사용하여 Azure AD 모듈에 로그인합니다. 

1. 맞는 앱이 있는지 확인하고 *8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4*를 앞의 섹션에서 복사한 ObjectID로 바꿉니다.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4.
    ```

 이제 앱을 확인했으므로 다음과 같이 홈 페이지를 업데이트할 준비가 되었습니다.

2. 변경 내용을 저장하기 위해 비어 있는 응용 프로그램 개체를 만듭니다. 이 변수는 업데이트하려는 값을 보유합니다. 이 단계에서는 아무 것도 만들어지지 않습니다.

    ```
    $appnew = New-Object “Microsoft.Open.AzureAD.Model.Application”
    ```

3. 홈페이지 URL을 원하는 값으로 설정합니다. 값은 게시된 앱의 하위 도메인 경로여야 합니다. 예를 들어 홈페이지 URL을 *https://sharepoint-iddemo.msappproxy.net/* 에서 *https://sharepoint-iddemo.msappproxy.net/hybrid/* 로 변경하면 앱 사용자가 사용자 지정 홈페이지로 바로 이동합니다.

    ```
    $homepage = “https://sharepoint-iddemo.msappproxy.net/hybrid/”
    ```
4. "1단계: 앱의 ObjectID 찾기"에서 복사한 GUID(ObjectID)를 사용하여 업데이트합니다.

    ```
    Set-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4 -Homepage $homepage
    ```
5. 변경 내용이 성공했는지를 확인하려면 앱을 다시 시작합니다.

    ```
    Get-AzureADApplication -ObjectId 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
    ```

>[!NOTE]
>앱에 대한 변경 내용으로 인해 홈페이지 URL을 다시 설정할 수도 있습니다. 홈 페이지 URL을 다시 설정하는 경우 이 섹션의 단계를 반복하여 재설정합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 응용 프로그램 프록시를 사용하여 SharePoint에 원격 액세스 사용하도록 설정](application-proxy-integrate-with-sharepoint-server.md)
- [Azure Portal에서 응용 프로그램 프록시 사용](application-proxy-enable.md)
