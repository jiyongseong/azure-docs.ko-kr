---
title: Azure Event Grid 및 Event Hubs 통합
description: Azure Event Grid 및 Event Hubs를 사용하여 SQL Data Warehouse로 데이터를 마이그레이션하는 방법 설명
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: tutorial
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 41cd2f1081cbe8d8fca9d6afa77b87f9aa1017d3
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302942"
---
# <a name="stream-big-data-into-a-data-warehouse"></a>데이터 웨어하우스로 빅 데이터 스트림

Azure [Event Grid](overview.md)는 앱과 서비스의 알림에 응답하는 데 사용할 수 있는 지능형 이벤트 라우팅 서비스입니다. [Event Hubs 캡처 및 Event Grid 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)에는 Azure Event Hubs 캡처와 Azure Event Grid를 사용하여 이벤트 허브에서 SQL Data Warehouse로 데이터를 원활하게 마이그레이션하는 방법이 나와 있습니다.

![응용 프로그램 개요](media/event-grid-event-hubs-integration/overview.png)

데이터가 이벤트 허브로 전송되면 캡처가 스트림에서 데이터를 가져온 다음 해당 데이터가 포함된 Storage Blob을 Avro 형식으로 생성합니다. 캡처는 Blob을 생성할 때 이벤트를 트리거합니다. Event Grid는 이벤트에 대한 데이터를 구독자에게 배포합니다. 이때 이벤트 데이터는 Azure Functions 끝점으로 전송됩니다. 이벤트 데이터는 생성된 Blob의 경로를 포함합니다. 함수는 해당 URL을 사용하여 파일을 검색한 다음 데이터 웨어하우스로 전송합니다.

이 문서에서는 다음 작업을 수행합니다.

* 다음 인프라 배포
  * 캡처를 사용하도록 설정한 이벤트 허브
  * 캡처의 파일용 저장소 계정
  * 함수 앱 호스팅용 Azure App Service 계획
  * 이벤트 처리용 함수 앱
  * 데이터 웨어하우스 호스팅용 SQL Server
  * 마이그레이션된 데이터 저장용 SQL Data Warehouse
* 데이터 웨어하우스에 테이블 만들기
* 함수 앱에 코드 추가
* 이벤트 구독
* 이벤트 허브로 데이터를 보내는 앱 실행
* 데이터 웨어하우스에서 마이그레이션된 데이터 보기

## <a name="about-the-event-data"></a>이벤트 데이터 정보

Event Grid는 구독자에게 이벤트 데이터를 배포합니다. 다음 예제에서는 캡처 파일을 만들기 위한 이벤트 데이터를 보여 줍니다. 특히 `data` 개체의 `fileUrl` 속성을 자세히 확인하세요. 함수 앱은 이 값을 가져와 캡처 파일을 검색하는 데 사용합니다.

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        }
    }
]
```

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 항목이 필요합니다.

* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* .NET 데스크톱 개발, Azure 개발, ASP.NET 및 웹 개발, Node.js 개발, Python 개발용 작업이 포함된 [Visual Studio 2017 버전 15.3.2 이상](https://www.visualstudio.com/vs/)
* 컴퓨터에 다운로드한 [EventHubsCaptureEventGridDemo 샘플 프로젝트](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)

## <a name="deploy-the-infrastructure"></a>인프라 배포

이 문서의 작업을 간편하게 수행하려면 Resource Manager 템플릿을 사용하여 필요한 인프라를 배포합니다. 배포되는 리소스를 보려면 [템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json)을 확인하세요. 리소스 그룹 위치로 [지원되는 지역](overview.md) 중 하나를 사용합니다.

Azure CLI의 경우 

```azurecli-interactive
az group create -l westcentralus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

PowerShell의 경우 다음을 사용합니다.

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```

메시지가 표시되면 암호 값을 입력합니다.

## <a name="create-a-table-in-sql-data-warehouse"></a>SQL Data Warehouse에서 테이블 만들기

[CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) 스크립트를 실행하여 데이터 웨어하우스에 테이블을 추가합니다. 스크립트를 실행하려면 Portal의 쿼리 편집기나 Visual Studio를 사용합니다.

실행할 스크립트는 다음과 같습니다.

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-the-azure-functions-app"></a>Azure Functions 앱 게시

1. Visual Studio 2017(15.3.2 이상)에서 [EventHubsCaptureEventGridDemo 샘플 프로젝트](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)를 엽니다.

1. 솔루션 탐색기에서 **FunctionEGDWDumper**를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

   ![함수 앱 게시](media/event-grid-event-hubs-integration/publish-function-app.png)

1. **Azure 함수 앱**과 **기존 항목 선택**을 차례로 선택합니다. **게시**를 선택합니다.

   ![대상 함수 앱](media/event-grid-event-hubs-integration/pick-target.png)

1. 템플릿을 통해 배포한 함수 앱을 선택합니다. **확인**을 선택합니다.

   ![함수 앱 선택](media/event-grid-event-hubs-integration/select-function-app.png)

1. Visual Studio에서 프로필이 구성되었으면 **게시**를 선택합니다.

   ![게시 선택](media/event-grid-event-hubs-integration/select-publish.png)

함수를 게시한 후에는 이벤트를 구독할 수 있습니다.

## <a name="subscribe-to-the-event"></a>이벤트 구독

1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 리소스 그룹 및 함수 앱을 선택합니다.

   ![함수 앱 보기](media/event-grid-event-hubs-integration/view-function-app.png)

1. 함수를 선택합니다.

   ![함수 선택](media/event-grid-event-hubs-integration/select-function.png)

1. **Event Grid 구독 추가**를 선택합니다.

   ![구독 추가](media/event-grid-event-hubs-integration/add-event-grid-subscription.png)

9. Event Grid 구독의 이름을 지정합니다. 이벤트 형식으로 **Event Hubs 네임스페이스**를 사용합니다. 값을 입력하여 Event Hubs 네임스페이스의 인스턴스를 선택합니다. 구독자 엔드포인트에서는 입력된 값을 그대로 둡니다. **만들기**를 선택합니다.

   ![구독 만들기](media/event-grid-event-hubs-integration/set-subscription-values.png)

## <a name="run-the-app-to-generate-data"></a>앱을 실행하여 데이터 생성

이벤트 허브, SQL 데이터 웨어하우스, Azure 함수 앱 및 이벤트 구독 설정이 완료되었습니다. 이제 솔루션이 이벤트 허브에서 데이터 웨어하우스로 데이터를 마이그레이션할 준비가 되었습니다. 이벤트 허브에 대한 데이터를 생성하는 응용 프로그램을 실행하기 전에 몇 가지 값을 구성해야 합니다.

1. Portal에서 이벤트 허브 네임스페이스를 선택합니다. **연결 문자열**을 선택합니다.

   ![연결 문자열 선택](media/event-grid-event-hubs-integration/event-hub-connection.png)

2. **RootManageSharedAccessKey** 선택

   ![키 선택](media/event-grid-event-hubs-integration/show-root-key.png)

3. **연결 문자열 - 기본 키** 복사

   ![키 복사](media/event-grid-event-hubs-integration/copy-key.png)

4. Visual Studio 프로젝트로 돌아옵니다. WindTurbineDataGenerator 프로젝트에서 **program.cs**를 엽니다.

5. 두 상수 값을 바꿉니다. **EventHubConnectionString**에는 복사한 값을 사용하고, Event Hubs 이름으로 **hubdatamigration**을 사용합니다.

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. 솔루션을 빌드하십시오. WindTurbineGenerator.exe 응용 프로그램을 실행합니다. 몇 분 후에 데이터 웨어하우스에서 마이그레이션된 데이터용 테이블을 쿼리합니다.

## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
* Event Hubs 캡처에 대한 소개는 [Azure Portal을 사용하여 Event Hubs 캡처를 사용하도록 설정](../event-hubs/event-hubs-capture-enable-through-portal.md)을 참조하세요.
* 샘플 설정 및 실행에 대한 자세한 내용은 [Event Hubs 캡처 및 Event Grid 샘플](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)을 참조하세요.