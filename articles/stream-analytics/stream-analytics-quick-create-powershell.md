---
title: Azure PowerShell을 사용하여 Stream Analytics 작업 만들기
description: 이 빠른 시작에서는 Azure PowerShell 모듈을 사용하여 Azure Stream Analytics 작업을 배포하고 실행하는 방법을 자세히 설명합니다.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 2b5d8bfd6dbe36637a0c6873e941118e7ee71b80
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212435"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Stream Analytics 작업 만들기

Azure PowerShell 모듈은 PowerShell cmdlet 또는 스크립트를 사용하여 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 빠른 시작에서는 Azure PowerShell 모듈을 사용하여 Azure Stream Analytics 작업을 배포하고 실행하는 방법을 자세히 설명합니다. 

예제 작업에서는 Azure blob 저장소의 blob에서 스트리밍 데이터를 읽습니다. 이 빠른 시작에 사용된 입력 데이터 파일에는 오직 설명할 목적으로 정적 데이터가 들어 있습니다. 실제 시나리오에서는 Stream Analytics 작업에 스트리밍 입력 데이터를 사용합니다. 그런 다음, 작업이 Stream Analytics 쿼리 언어를 사용하여 데이터를 변환하여 100°가 넘는 경우의 평균 온도를 계산합니다. 마지막으로, 결과 출력 이벤트를 다른 파일에 씁니다. 

## <a name="before-you-begin"></a>시작하기 전에

* Azure 구독이 아직 없는 경우 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.  

* 이 빠른 시작에서는 Azure PowerShell 모듈 버전 3.6 이상이 필요합니다. 로컬 컴퓨터에 설치된 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) 문서를 참조하세요. 


## <a name="sign-in-to-azure"></a>Azure에 로그인

`Connect-AzureRmAccount` 명령을 사용하여 Azure 구독에 로그인하고 팝업 브라우저에 Azure 자격 증명을 입력합니다.

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

구독이 여러 개인 경우, 로그인 한 후 아래 cmdlet을 실행하여 빠른 시작에 사용할 구독을 선택합니다. <your subscription name>을 구독의 이름으로 바꿔야 합니다.  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>입력 데이터 준비

Stream Analytics 작업을 정의하기 전에 작업에 대한 입력으로 구성된 데이터를 준비합니다.

1. GitHub에서 [센서 샘플 데이터](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)를 다운로드합니다. 링크를 마우스 오른쪽 단추로 클릭하고 **링크를 다른 이름으로 저장...**  또는 **대상을 다른 이름으로 저장**합니다.

2. 다음 PowerShell 코드 블록은 여러 명령을 수행하여 작업에 필요한 입력 데이터를 준비합니다. 코드를 이해하려면 섹션을 검토합니다. 

   1. [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount) cmdlet을 사용하여 표준 범용 저장소 계정을 만듭니다.  이 예제에서는 LRS(로컬 중복 저장소) 및 Blob 암호화(기본적으로 사용)를 사용하여 mystorageaccount라는 저장소 계정을 만듭니다.  

   2. 사용할 저장소 계정을 정의하는 저장소 계정 컨텍스트 `$storageAccount.Context`를 검색합니다. 저장소 계정으로 작업할 때 자격 증명을 반복적으로 제공하는 대신 컨텍스트를 참조합니다. 

   3. [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer)를 사용하여 저장소 컨테이너를 만들고 이전에 다운로드한 [센서 샘플 데이터](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)를 업로드합니다. 

   4. 코드로 출력되는 저장소 키를 복사하고 나중에 스트리밍 작업의 입력 및 출력을 만들 때 해당 키를 JSON 파일에 붙여넣습니다.

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

[New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0) cmdlet을 사용하여 Stream Analytics 작업을 만듭니다. 이 cmdlet은 작업 이름, 리소스 그룹 이름 및 작업 정의를 매개 변수로 사용합니다. 작업 이름은 작업을 식별하는 친숙한 이름일 수 있습니다. 영숫자 문자, 하이픈 및 밑줄만 포함할 수 있으며 길이는 3자에서 63자 사이여야 합니다. 작업 정의는 작업을 만드는 데 필요한 속성을 포함하는 JSON 파일입니다. 로컬 컴퓨터에서 `JobDefinition.json`이라는 파일을 만들고 여기에 다음 JSON 데이터를 추가합니다.

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

그런 다음, `New-AzureRmStreamAnalyticsJob` cmdlet을 실행합니다. `jobDefinitionFile` 변수의 값을 작업 정의 JSON 파일을 저장한 경로로 바꾸어야 합니다. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>작업에 대한 입력 구성

[New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0) cmdlet을 사용하여 작업에 입력을 추가합니다. 이 cmdlet은 작업 이름, 작업 입력 이름, 리소스 그룹 이름 및 작업 입력 정의를 매개 변수로 사용합니다. 작업 입력 정의는 작업의 입력을 구성하는 데 필요한 속성을 포함하는 JSON 파일입니다. 이 예제에서는 입력으로 blob 저장소를 만듭니다. 

로컬 컴퓨터에서 `JobInputDefinition.json`이라는 파일을 만들고 여기에 다음 JSON 데이터를 추가합니다. `accountKey`의 값을 $storageAccountKey 값에 저장된 값인 저장소 계정의 액세스 키로 바꾸어야 합니다. 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

그런 다음, `New-AzureRmStreamAnalyticsInput` cmdlet을 실행하고 `jobDefinitionFile` 변수의 값을 작업 입력 정의 JSON 파일을 저장한 경로로 바꾸어야 합니다. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>작업에 대한 출력 구성

[New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0) cmdlet을 사용하여 작업에 대한 출력을 추가합니다. 이 cmdlet은 작업 이름, 작업 출력 이름, 리소스 그룹 이름 및 작업 출력 정의를 매개 변수로 사용합니다. 작업 출력 정의는 작업 출력을 구성하는 데 필요한 속성을 포함하는 JSON 파일입니다. 이 예제에서는 출력으로 blob 저장소를 사용합니다. 

로컬 컴퓨터에서 `JobOutputDefinition.json`이라는 파일을 만들고 여기에 다음 JSON 데이터를 추가합니다. `accountKey`의 값을 $storageAccountKey 값에 저장된 값인 저장소 계정의 액세스 키로 바꾸어야 합니다. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

그런 다음, `New-AzureRmStreamAnalyticsOutput` cmdlet을 실행합니다. `jobOutputDefinitionFile` 변수의 값을 작업 출력 정의 JSON 파일을 저장한 경로로 바꾸어야 합니다. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>변환 쿼리 정의

[New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0) cmdlet을 사용하여 작업에 변환을 추가합니다. 이 cmdlet은 작업 이름, 작업 변환 이름, 리소스 그룹 이름 및 작업 변환 정의를 매개 변수로 사용합니다. 로컬 컴퓨터에서 `JobTransformationDefinition.json`이라는 파일을 만들고 여기에 다음 JSON 데이터를 추가합니다. JSON 파일에는 변환 쿼리를 정의하는 쿼리 매개 변수가 포함됩니다.

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

그런 다음, `New-AzureRmStreamAnalyticsTransformation` cmdlet을 실행합니다. `jobTransformationDefinitionFile` 변수의 값을 작업 출력 정의 JSON 파일을 저장한 경로로 바꾸어야 합니다. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Stream Analytics 작업을 시작하고 출력을 확인합니다.

[Start-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) cmdlet을 사용하여 작업을 시작합니다. 이 cmdlet은 작업 이름, 리소스 그룹 이름, 출력 시작 모드 및 시작 시간을 매개 변수로 사용합니다. `OutputStartMode`는 `JobStartTime`, `CustomTime` 또는 `LastOutputEventTime`의 값을 허용합니다. 이러한 각 값에 대한 자세한 내용은 PowerShell 설명서의 [매개 변수](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) 섹션을 참조하세요. 이 예제에서는 모드를 `CustomTime`으로 지정하고 `OutputStartTime`에 값을 지정합니다. 

시간 값은 `2018-01-01`을 선택합니다. 이 시작 날짜가 선택된 것은 샘플 데이터의 이벤트 타임스탬프보다 앞서기 때문입니다. 다음 cmdlet을 실행한 후 작업이 시작되면 `True`가 출력으로 반환됩니다. 저장소 컨테이너에서 변환된 데이터를 사용하여 출력 폴더가 생성됩니다. 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 스트리밍 작업 및 모든 관련 리소스를 삭제합니다. 작업을 삭제하면 작업에서 사용된 스트리밍 단위에 대한 청구를 방지합니다. 작업을 나중에 사용하려는 경우 삭제를 건너뛰고 지금은 작업을 중지할 수 있습니다. 이 작업을 계속 사용하지 않으려면 다음과 같은 cmdlet을 실행하여 빠른 시작에서 만든 리소스를 모두 삭제합니다.

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 간단한 Stream Analytics 작업을 배포했습니다. 다른 입력 원본을 구성하고 실시간 검색을 수행하는 방법을 알아보려면 다음 문서로 이동하세요.

> [!div class="nextstepaction"]
> [Azure Stream Analytics를 사용하여 실시간 부정 행위 감지](stream-analytics-real-time-fraud-detection.md)
