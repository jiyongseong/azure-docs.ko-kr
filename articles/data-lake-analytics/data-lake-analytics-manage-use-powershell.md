---
title: Azure PowerShell을 사용하여 Azure Data Lake Analytics 관리 | Microsoft Docs
description: 'Data Lake Analytics 계정, 데이터 원본, 작업 및 카탈로그 항목을 관리하는 방법을 알아봅니다. '
services: data-lake-analytics
documentationcenter: ''
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: 96360eabefcbbdf36ef3bd83b0c6de45c1a6f3cc
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
ms.locfileid: "33205247"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 관리
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure PowerShell을 사용하여 Azure Data Lake Analytics 계정, 데이터 원본, 작업 및 카탈로그 항목을 관리하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 조건

Data Lake Analytics 계정을 만들 때는 다음을 알아두어야 합니다.

* **구독 ID**: Data Lake Analytics 계정이 상주하는 Azure 구독 ID입니다.
* **리소스 그룹**: Data Lake Analytics 계정이 포함된 Azure 리소스 그룹의 이름입니다.
* **Data Lake Analytics 계정 이름**: 이 계정 이름은 소문자와 숫자만을 포함해야 합니다.
* **기본 Data Lake Store 계정**: 각 Data Lake Analytics 계정에는 기본 Data Lake Store 계정이 있습니다. 이러한 계정은 동일한 위치에 있어야 합니다.
* **위치**: Data Lake Analytics 계정의 위치입니다(예: "미국 동부 2" 또는 다른 지원되는 위치). 지원되는 위치는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-lake-analytics/)에서 확인할 수 있습니다.

이 자습서의 PowerShell 코드 조각은 이러한 변수를 사용하여 이 정보를 저장합니다.

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>로그인

구독 ID를 사용하여 로그인합니다.

```powershell
Connect-AzureRmAccount -SubscriptionId $subId
```

구독 이름을 사용하여 로그인합니다.

```
Connect-AzureRmAccount -SubscriptionName $subname 
```

`Connect-AzureRmAccount` cmdlet은 항상 자격 증명을 묻는 메시지를 표시합니다. 다음 cmdlet을 사용하면 메시지가 표시되지 않게 할 수 있습니다.

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="manage-accounts"></a>계정 관리

### <a name="create-a-data-lake-analytics-account"></a>Data Lake 분석 계정 만들기

사용할 [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups)이 아직 없는 경우 만듭니다. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

모든 Data Lake Analytics 계정에는 로그를 저장하는 데 사용할 기본 Data Lake Store 계정이 필요합니다. 기존 계정을 다시 사용하거나 새 계정을 만들 수 있습니다. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

리소스 그룹 및 Data Lake Store 계정을 사용할 수 있게 되면 Data Lake Analytics 계정을 만듭니다.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-acount-information"></a>계정 정보 가져오기

계정에 대한 세부 정보를 가져옵니다.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

특정 Data Lake Analytics 계정의 존재 여부를 확인합니다. 이 cmdlet은 `$true` 또는 `$false`를 반환합니다.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

특정 Data Lake Store 계정이 있는지 확인합니다. 이 cmdlet은 `$true` 또는 `$false`를 반환합니다.

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="list-accounts"></a>계정 나열

현재 구독 내의 Data Lake Analytics 계정을 나열합니다.

```powershell
Get-AdlAnalyticsAccount
```

특정 리소스 그룹 내의 Data Lake Analytics 계정을 나열합니다.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="manage-data-sources"></a>데이터 원본 관리
Azure Data Lake Analytics는 현재 다음 데이터 원본을 지원합니다.

* [Azure Data Lake Storage](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Analytics 계정을 만들 때 Data Lake Store 계정이 기본 데이터 원본이 되도록 지정해야 합니다. 기본 데이터 레이크 저장소 계정은 작업 메타데이터 및 작업 감사 로그를 저장하는 데 사용됩니다. Data Lake Analytics 계정을 만든 후 Data Lake Store 계정 및/또는 Azure Storage 계정을 더 추가할 수 있습니다. 

### <a name="find-the-default-data-lake-store-account"></a>기본 데이터 레이크 저장소 계정 찾기

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

`IsDefault` 속성으로 데이터 원본 목록을 필터링하여 기본 Data Lake Store 계정을 찾을 수 있습니다.

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>데이터 원본 추가

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>데이터 원본 나열

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>U-SQL 작업 제출

### <a name="submit-a-string-as-a-u-sql-script"></a>문자열을 U-SQL 스크립트로 제출

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-script"></a>파일을 U-SQL 스크립트로 제출

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>계정에 작업 나열

### <a name="list-all-the-jobs-in-the-account"></a>계정에서 모든 작업을 나열합니다. 

출력은 현재 실행 중인 작업 및 최근에 완료된 해당 작업을 포함합니다.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>상위 N개 작업 나열

기본적으로 작업 목록은 제출 시간을 기준으로 정렬됩니다. 따라서 최근에 제출한 작업이 먼저 표시됩니다. ADLA 계정은 기본적으로 작업을 180일 동안 기억하지만, Ge-AdlJob cmdlet은 기본적으로 처음 500개 작업만 반환합니다. 특정 수의 작업을 나열하려면 -Top 매개 변수를 사용합니다.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-based-on-the-value-of-job-property"></a>작업 속성 값을 기준으로 작업 나열

`-State` 매개 변수 사용. 이러한 값을 결합할 수 있습니다.

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

`-Result` 매개 변수를 사용하여 종료된 작업이 성공적으로 완료되었는지 여부를 검색합니다. 다음 값을 포함합니다.

* Cancelled
* 실패
* 없음
* Succeeded

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

`-Submitter` 매개 변수를 사용하면 작업을 제출한 사람을 식별할 수 있습니다.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

`-SubmittedAfter`는 시간 범위로 필터링하는 데 유용합니다.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>작업 기록 분석

Azure PowerShell을 사용하여 Data Lake 분석에서 실행된 작업의 기록을 분석하는 것은 강력한 기술입니다. 사용량 및 비용에 대해 이해하는 데 사용할 수 있습니다. [작업 기록 분석 샘플 리포지토리](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)를 확인하여 자세히 알아볼 수 있습니다.  

## <a name="get-information-about-pipelines-and-recurrences"></a>파이프라인 및 되풀이에 대한 정보 가져오기

`Get-AdlJobPipeline` cmdlet을 사용하여 이전에 제출한 작업의 파이프라인 정보를 확인합니다.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

`Get-AdlJobRecurrence` cmdlet을 사용하여 이전에 제출한 작업의 되풀이 정보를 확인합니다.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="get-information-about-a-job"></a>작업 정보 가져오기

### <a name="get-job-status"></a>작업 상태 가져오기

특정 작업의 상태를 가져옵니다.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>작업 출력 검토

작업이 종료되면 폴더의 파일을 나열하여 출력 파일이 있는지 확인합니다.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>실행 중인 작업 관리

### <a name="cancel-a-job"></a>작업 취소

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>작업이 완료될 때까지 대기

작업이 완료될 때까지 `Get-AdlAnalyticsJob`을 반복하는 대신 `Wait-AdlJob` cmdlet을 사용하여 작업이 종료될 때까지 기다릴 수 있습니다.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>계산 정책 관리

### <a name="list-existing-compute-policies"></a>기존 계산 정책 나열

`Get-AdlAnalyticsComputePolicy` cmdlet은 Data Lake Analytics 계정에 대한 계산 정책에 대한 정보를 검색합니다.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>계산 정책 만들기

`New-AdlAnalyticsComputePolicy` cmdlet은 Data Lake Analytics 계정에 대한 새 계산 정책을 만듭니다. 이 예제에서는 지정된 사용자에게 제공되는 최대 AU를 50으로 설정하고 최소 작업 우선 순위를 250으로 설정합니다.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>파일의 존재 여부를 확인합니다.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>업로드 및 다운로드

파일을 업로드합니다.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

전체 폴더를 재귀적으로 업로드합니다.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

파일을 다운로드합니다.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

전체 폴더를 재귀적으로 다운로드합니다.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> 업로드 또는 다운로드 프로세스가 중단될 경우 ``-Resume`` 플래그와 함께 해당 cmdlet을 다시 실행하여 프로세스를 다시 시작할 수 있습니다.

## <a name="manage-catalog-items"></a>카탈로그 항목 관리

U-SQL 카탈로그는 U-SQL 스크립트에서 공유할 수 있도록 데이터 및 코드를 구성하는 데 사용됩니다. 카탈로그를 사용하면 가능한 가장 높은 성능으로 Azure 데이터 레이크의 데이터를 사용할 수 있습니다. 자세한 내용은 [U-SQL 카탈로그 사용](data-lake-analytics-use-u-sql-catalog.md)을 참조하세요.

### <a name="list-items-in-the-u-sql-catalog"></a>U-SQL 카탈로그의 항목 나열

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

ADLA 계정에 있는 모든 데이터베이스의 모든 어셈블리를 나열합니다.

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>카탈로그 항목에 대한 세부 정보 가져오기

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-credentials-in-a-catalog"></a>카탈로그에 자격 증명 만들기

U-SQL 데이터베이스 내에서 Azure에서 호스트되는 데이터베이스에 대한 자격 증명 개체를 만듭니다. 현재 U-SQL 자격 증명은 PowerShell을 통해 만들 수 있는 유일한 유형의 카탈로그 항목입니다.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

### <a name="get-basic-information-about-an-adla-account"></a>ADLA 계정에 대한 기본 정보 가져오기

계정 이름을 지정하는 경우 다음 코드는 계정에 대한 기본 정보를 조회합니다.

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="manage-firewall-rules"></a>방화벽 규칙 관리

### <a name="list-firewall-rules"></a>방화벽 규칙 나열

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>방화벽 규칙 추가

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="change-a-firewall-rule"></a>방화벽 규칙 변경

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>방화벽 규칙 제거

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Azure IP 주소를 허용합니다.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Azure 작업

### <a name="get-details-of-azurerm-errors"></a>AzureRm 오류에 대한 세부 정보 가져오기

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>관리자로 실행 중인지 확인

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>TenantID 찾기

구독 이름에서

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

구독 ID에서

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

"contoso.com"과 같은 도메인 주소에서


```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>모든 구독 및 테넌트 ID 나열

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>템플릿을 사용하여 Data Lake Analytics 계정 만들기

[템플릿을 사용하여 Data Lake Analytics 계정 만들기](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template) 샘플을 사용하는 Azure 리소스 그룹 템플릿을 사용할 수도 있습니다.


## <a name="next-steps"></a>다음 단계
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md)을 사용하여 Data Lake Analytics 시작
* [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md)를 사용하여 Azure Data Lake Analytics 관리 
