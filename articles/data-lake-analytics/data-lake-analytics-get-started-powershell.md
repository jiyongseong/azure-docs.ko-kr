---
title: Azure PowerShell을 사용하여 Azure Data Lake Analytics 시작 | Microsoft Docs
description: 'Azure PowerShell을 사용하여 Data Lake Analytics 계정을 만들고, U-SQL을 사용하여 Data Lake Analytics 작업을 만들고, 작업을 제출하는 방법에 대해 알아봅니다. '
services: data-lake-analytics
documentationcenter: ''
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: saveenr
ms.openlocfilehash: 332b6c90ea51d16a439bfb21222bb753e93a02b9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2018
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Data Lake Analytics 시작
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure PowerShell을 사용하여 Azure Data Lake Analytics 계정을 만든 다음 U-SQL 작업을 제출하고 실행하는 방법에 대해 알아봅니다. 데이터 레이크 분석에 대한 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음 정보가 있어야 합니다.

* **Azure 데이터 레이크 분석 계정**. [Data Lake Analytics 시작](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)을 참조하세요.
* **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인

이 자습서에서는 Azure PowerShell을 사용하는 것에 이미 익숙하다고 가정합니다. 특히, Azure에 로그인하는 방법을 알아야 합니다. 도움이 필요한 경우 [Azure PowerShell 시작](https://docs.microsoft.com/powershell/azure/get-started-azureps)을 참조하세요.

구독 이름을 사용하여 로그인하려면

```
Connect-AzureRmAccount -SubscriptionName "ContosoSubscription"
```

구독 이름 대신 구독 ID를 사용하여 로그인할 수도 있습니다.

```
Connect-AzureRmAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

성공하면 이 명령의 출력은 다음 텍스트와 유사합니다.

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>자습서 준비

이 자습서의 PowerShell 코드 조각은 이러한 변수를 사용하여 이 정보를 저장합니다.

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Data Lake Analytics 계정에 대한 정보 가져오기

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>U-SQL 작업 제출

U-SQL 스크립트를 보유할 PowerShell 변수를 만듭니다.

```
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

`Submit-AdlJob` cmdlet 및 `-Script` 매개 변수를 사용하여 스크립트 텍스트를 제출합니다.

```
$job = Submit-AdlJob -Account $adla -Name "My Job" –Script $script
```

또는 `-ScriptPath` 매개 변수를 사용하여 스크립트 파일을 제출할 수 있습니다.

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -Account $adla -Name "My Job" –ScriptPath $filename
```

`Get-AdlJob`을 사용하여 작업 상태를 불러옵니다. 

```
$job = Get-AdlJob -Account $adla -JobId $job.JobId
```

작업이 완료될 때까지 Get-AdlJob을 반복해서 호출하는 대신 `Wait-AdlJob` cmdlet을 사용합니다.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

`Export-AdlStoreItem`을 사용하여 출력 파일을 다운로드합니다.

```
Export-AdlStoreItem -Account $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>참고 항목
* 다른 도구를 사용하여 같은 자습서를 보려면 페이지 맨 위의 탭 선택기를 클릭합니다.
* U-SQL을 알아보려면 [Azure Data Lake Analytics U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.
* 관리 작업을 보려면 [Azure Portal을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)를 참조하세요.
