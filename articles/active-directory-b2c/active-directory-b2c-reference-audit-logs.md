---
title: 'Azure Active Directory B2C: 감사 로그 샘플 및 정의'
description: Azure AD B2C 감사 로그에 액세스하는 방법에 대한 가이드 및 샘플
services: active-directory-b2c
author: sromeroz
manager: sasubram
ms.author: sezambra
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.openlocfilehash: 8cc48853b0677230c4e19df4f0ecd93ce88b119d
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206452"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Azure AD B2C 감사 로그 액세스

Azure AD B2C(Azure Active Directory B2C)는 B2C 리소스, 발행된 토큰 및 관리자 액세스와 관련하여 활동 정보가 포함된 감사 로그를 전송합니다. 이 문서에서는 감사 로그를 통해 사용할 수 있는 정보 및 Azure AD B2C 테넌트에 대해 이 데이터에 액세스하는 방법에 대한 지침을 간략히 제공합니다.

> [!IMPORTANT]
> 감사 로그는 7일 동안만 유지됩니다. 보존 기간이 더 오래 필요한 경우, 아래 표시된 방법 중 하나를 사용하여 로그를 다운로드하고 저장하도록 플랜하세요. 

##<a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>감사 로그의 B2C 범주에서 사용 가능한 활동 개요
감사 로그의 **B2C** 범주에는 다음 유형의 활동이 포함됩니다.
|활동 유형 |설명  |
|---------|---------|
|권한 부여 |B2C 리소스에 액세스하는 사용자(예: B2C 정책 목록에 액세스하는 관리자)에 대한 권한 부여와 관련된 활동         |
|디렉터리 |관리자가 Azure Portal을 사용하여 로그인할 때 검색되는 디렉터리 특성과 관련된 활동 |
|응용 프로그램 | B2C 응용 프로그램에 대한 CRUD 작업 |
|키 |B2C 키 컨테이너에 저장된 키에 대한 CRUD 작업 |
|리소스 |B2C 리소스에 대한 CRUD 작업(예: 정책 및 ID 공급자)
|인증 |사용자 자격 증명 및 토큰 발행에 대한 유효성 검사|

> [!NOTE]
> 사용자 개체 CRUD 활동의 경우 **핵심 디렉터리** 범주를 참조하세요.

##<a name="example-activity"></a>예제 활동
아래 예는 사용자가 외부 ID 공급자로 로그인할 때 캡처된 데이터를 보여 줍니다. ![감사 로그 - 예제](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

##<a name="accessing-audit-logs-through-the-azure-portal"></a>Azure Portal을 통해 감사 로그 액세스
1. [Azure 포털](https://portal.azure.com)로 이동합니다. B2C 디렉터리에 있는지 확인합니다.
2. 왼쪽의 즐겨찾기 막대에서 **Azure Active Directory**를 클릭합니다. 
    
    ![감사 로그 - AAD 단추](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. **활동** 아래에서 **감사 로그**를 클릭합니다.

    ![감사 로그 - 로그 섹션](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. **범주** 드롭 상자에서 **B2C**를 선택합니다.
3. **적용**을 클릭합니다.

    ![감사 로그 - 범주](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

최근 7일 동안 기록된 활동 목록이 표시됩니다. 
- **활동 리소스 종류** 드롭다운을 사용하여 위에 요약된 활동 유형별로 필터링합니다.
- **날짜 범위** 드롭다운을 사용하여 표시된 활동의 날짜 범위를 필터링합니다.
- 목록의 특정 행을 클릭하면 오른쪽에 있는 상황별 상자에 활동과 연관된 추가 특성이 표시됩니다.
- **다운로드**를 클릭하여 활동을 csv 파일로 다운로드합니다.

##<a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Azure AD 보고 API를 통해 감사 로그 액세스
감사 로그는 Azure Active Directory에 대한 다른 활동과 동일한 파이프라인에 게시되므로 [Azure Active Directory 보고 API](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-api-audit-reference)를 통해 액세스할 수 있습니다. 

###<a name="prerequisites"></a>필수 조건
Azure AD 보고 API를 인증하려면 먼저 응용 프로그램을 등록해야 합니다. [Azure AD Reporting API에 액세스하기 위한 필수 구성 요소](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)의 단계를 완료해야 합니다.

###<a name="accesing-the-api"></a>API 액세스
API를 통해 Azure AD B2C 감사 로그를 다운로드하려면 로그를 **B2C** 범주로 필터링합니다. 범주별로 필터링하려면 Azure AD 보고 API 끝점을 호출할 때 아래와 같이 쿼리 문자열 매개 변수를 사용합니다.

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

###<a name="powershell-script"></a>PowerShell 스크립트
다음 스크립트는 PowerShell을 사용하여 Azure AD 보고 API를 쿼리하고 결과를 JSON 파일로 저장하는 예를 보여 줍니다.

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"     
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {   
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago 

    # loop through each query page (1 through n)
    Do{
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

