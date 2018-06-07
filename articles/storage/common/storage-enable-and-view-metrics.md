---
title: Azure Portal에서 저장소 메트릭 사용 | Microsoft Docs
description: Blob, 큐, 테이블 및 파일 서비스의 저장소 메트릭을 활성화하는 방법에 대해 알아봅니다.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 0407adfc-2a41-4126-922d-b76e90b74563
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/14/2017
ms.author: rogarana
ms.openlocfilehash: 0caa4eff80877ad4bf8d501a276e82922b1a84c7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2018
---
# <a name="enabling-azure-storage-metrics-and-viewing-metrics-data"></a>Azure Storage 메트릭 사용 및 메트릭 데이터 보기
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>개요
새 Storage 계정을 만들 때 기본적으로 Storage 메트릭을 사용하도록 설정되어 있습니다. [Azure Portal](https://portal.azure.com)이나 Windows PowerShell을 통해 또는 저장소 클라이언트 라이브러리 중 하나를 통해 프로그래밍 방식으로 모니터링을 사용하도록 설정할 수 있습니다.

메트릭 데이터 보존 기간을 구성해야 합니다. 이 기간에 따라 저장소 서비스에서 메트릭을 보관하는 기간이 결정되며, 해당 메트릭을 저장하는 데 필요한 저장소에 대해 요금이 청구됩니다. 일반적으로는 시간 메트릭보다 분 메트릭에 더 짧은 보존 기간을 사용해야 합니다. 분 메트릭의 경우 추가 공간이 상당히 많이 필요하기 때문입니다. 데이터를 분석하고 오프라인 분석용 또는 보고용으로 보관할 메트릭을 다운로드할 시간이 충분하도록 보존 기간을 선택합니다. 저장소 계정에서 메트릭 데이터를 다운로드할 때도 요금이 청구됩니다.

## <a name="how-to-enable-metrics-using-the-azure-portal"></a>Azure Portal을 사용하여 메트릭을 사용하도록 설정하는 방법
[Azure Portal](https://portal.azure.com)에서 메트릭을 사용하도록 설정하려면 다음 단계를 따릅니다.

1. 저장소 계정으로 이동합니다.
1. **메뉴** 창에서 **진단**을 선택합니다.
1. **상태**가 **켜기**로 설정되어 있는지 확인합니다.
1. 모니터링하려는 서비스에 대한 메트릭을 선택합니다.
1. 보존 정책을 지정하여 메트릭 및 로그 데이터를 보존하는 기간을 나타냅니다.
1. **저장**을 선택합니다.

[Azure Portal](https://portal.azure.com)에서는 현재 저장소 계정에서 분 메트릭을 구성할 수 없습니다. 따라서 PowerShell을 사용하거나 프로그래밍 방식으로 분 메트릭을 사용하도록 설정해야 합니다.

## <a name="how-to-enable-metrics-using-powershell"></a>PowerShell을 사용하여 메트릭을 사용하도록 설정하는 방법
로컬 컴퓨터에서 PowerShell을 사용하여 저장소 계정의 저장소 메트릭을 구성할 수 있습니다. Azure PowerShell cmdlet Get-AzureStorageServiceMetricsProperty를 사용하여 현재 설정을 검색하고, Set-AzureStorageServiceMetricsProperty cmdlet을 사용하여 현재 설정을 변경합니다.

Storage 메트릭을 제어하는 cmdlet은 다음 매개 변수를 사용합니다.

* MetricsType: 사용 가능한 값은 Hour 및 Minute입니다.
* ServiceType: 사용 가능한 값은 Blob, Queue, Table입니다.
* MetricsLevel: 사용 가능한 값은 None, Service 및 ServiceAndApi입니다.

예를 들어 다음 명령은 보존 기간을 5일로 설정하여 기본 저장소 계정의 Blob service에 대해 분 메트릭을 설정합니다.

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5`
```

다음 명령은 기본 저장소 계정의 Blob 서비스에 대해 현재 시간 메트릭 수준 및 보존 기간(일)을 검색합니다.

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```

Azure 구독에서 작동하도록 Azure PowerShell cmdlet을 구성하고 사용할 기본 저장소 계정을 선택하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

## <a name="how-to-enable-storage-metrics-programmatically"></a>프로그래밍 방식으로 Storage 메트릭을 사용하도록 설정하는 방법
다음 C# 코드 조각은 .NET용 저장소 클라이언트 라이브러리를 사용하여 Blob service에 대한 로깅 및 메트릭을 사용하도록 설정하는 방법을 보여 줍니다.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days.
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>Storage 메트릭 보기
Storage 계정을 모니터링하도록 저장소 분석 메트릭을 구성하면 저장소 분석에서 Storage 계정의 알려진 테이블 집합에 메트릭을 기록합니다. [Azure Portal](https://portal.azure.com)에서 시간별 메트릭을 보도록 차트를 구성할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 저장소 계정으로 이동합니다.
1. 메트릭을 보려는 서비스에 대한 **메뉴** 창에서 **메트릭**을 선택합니다.
1. 구성하려는 차트에 대해 **편집**을 선택합니다.
1. **차트 편집** 창에서 **시간 범위**, **차트 종류** 및 차트에 표시할 메트릭을 선택합니다.
1. **확인**을 선택합니다.

장기 저장용 메트릭을 다운로드하거나 메트릭을 로컬에서 분석하려는 경우에는 다음을 수행해야 합니다.

* 이러한 테이블을 인식하고 있고 그것들을 보고 다운로드할 수 있도록 하는 도구를 사용합니다.
* 사용자 지정 응용 프로그램 또는 스크립트를 작성하여 테이블을 읽고 저장합니다.

대부분의 타사 저장소 찾아보기 도구는 이러한 테이블을 인식하며 테이블을 직접 보는 기능을 제공합니다.
사용 가능한 도구의 목록은 [Azure Storage 클라이언트 도구](storage-explorers.md)를 참조하세요.

> [!NOTE]
> [Microsoft Azure Storage Explorer](http://storageexplorer.com/) 버전 0.8.0부터 분석 및 메트릭 테이블을 보고 다운로드할 수 있습니다.
> 
> 

프로그래밍 방식으로 분석 테이블에 액세스하기 위해서는 저장소 계정의 모든 테이블을 나열하면 분석 테이블은 표시되지 않음에 유의하십시오. 테이블 이름을 쿼리하려면 이름을 통해 직접 액세스하거나 .NET 클라이언트 라이브러리에서 [CloudAnalyticsClient API](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.analytics.cloudanalyticsclient.aspx) 를 사용할 수 있습니다.

### <a name="hourly-metrics"></a>시간 메트릭
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>분 메트릭
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>용량
* $MetricsCapacityBlob

[저장소 분석 메트릭 테이블 스키마](https://msdn.microsoft.com/library/azure/hh343264.aspx)에서 이러한 테이블의 스키마에 대한 전체 세부 정보를 확인할 수 있습니다. 아래의 샘플 행에는 사용 가능한 열 중 일부만 나와 있습니다. 그러나 Storage 메트릭에서 이러한 메트릭을 저장하는 방식과 관련한 몇 가지 중요한 기능을 확인할 수 있습니다.

| PartitionKey | RowKey | 타임 스탬프 | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Availability | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |user;QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

이 예제 분 메트릭 데이터에서 파티션 키는 분 확인 시의 시간을 사용합니다. 행 키는 행에 저장되는 정보의 유형을 식별합니다. 행 키는 두 가지 정보, 액세스 형식 및 요청 형식으로 구성됩니다.

* 액세스 형식은 user 또는 system입니다. 여기서 user는 Storage 서비스에 대한 모든 사용자 요청을 지칭하며 system은 Storage 분석에서 수행한 요청을 지칭합니다.
* 요청 형식은 all(이 경우 요약 줄)이거나 QueryEntity 또는 UpdateEntity 등의 특정 API를 식별합니다.

위의 샘플 데이터는 오전 11시부터 1분 동안의 모든 레코드를 표시하므로 QueryEntities 요청의 수 + QueryEntity 요청의 수 + UpdateEntity 요청의 수 = 7입니다. 이 합계가 user:All 행에 표시됩니다. 마찬가지로 ((143.8 * 5) + 3 + 9)/7을 계산하여 user:All 행에 평균 종단 간 대기 시간인 104.4286을 표시할 수 있습니다.

## <a name="metrics-alerts"></a>메트릭 알림
Storage 메트릭이 Storage 서비스 동작의 중요한 변경 내용을 자동으로 알릴 수 있도록 [Azure Portal](https://portal.azure.com)에서 경고를 설정하는 것을 고려해야 합니다. 저장소 탐색기 도구를 사용하여 이 메트릭 데이터를 구분된 형식에서 다운로드하려면 Microsoft Excel을 사용하여 데이터를 분석할 수 있습니다. 사용 가능한 저장소 탐색기 도구의 목록은 [Azure Storage Client Tools](storage-explorers.md)를 참조하세요. 저장소 계정 메뉴 창에서 **모니터링** 아래에서 액세스할 수 있는 **경고 규칙** 창에서 알림을 구성할 수 있습니다.

> [!IMPORTANT]
> 저장소 이벤트와 해당 시간 또는 분 메트릭 데이터가 기록되는 시간 사이에 지연이 있을 수 있습니다. 분 메트릭을 로깅하는 경우 데이터의 몇 분을 한 번에 작성할 수 있습니다. 그러면 이전 분의 트랜잭션이 현재 분의 트랜잭션으로 집계될 수 있습니다. 이 경우 알림 서비스에는 구성된 알림 간격에 대한 사용 가능한 모든 메트릭 데이터가 없을 수 있어서 예기치 않게 알림이 발생할 수 있습니다.
>

## <a name="accessing-metrics-data-programmatically"></a>프로그래밍 방식으로 메트릭 데이터 액세스
다음 목록에서는 특정 시간(분) 범위에 대한 분 메트릭에 액세스하여 결과를 콘솔 창에 표시하는 샘플 C# 코드를 보여 줍니다. 이 코드는 저장소의 메트릭 테이블 액세스를 간소화하는 CloudAnalyticsClient 클래스가 포함된 Azure Storage 라이브러리 버전 4를 사용합니다.

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
{
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
        Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
        var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
        var t = analyticsClient.GetMinuteMetricsTable(service);
        var opContext = new OperationContext();
        var query =
          from entity in metricsQuery
          // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
          // because they are calculated fields in the MetricsEntity class.
          // The PartitionKey identifies the DataTime of the metrics.
          where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0
        select entity;

        // Filter on "user" transactions after fetching the metrics from Table Storage.
        // (StartsWith is not supported using LINQ with Azure table storage)
        var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
        var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
        Console.WriteLine(resultString);
    }
}

private static string MetricsString(MetricsEntity entity, OperationContext opContext)
{
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
      string.Format("Time: {0}, ", entity.Time) +
      string.Format("AccessType: {0}, ", entity.AccessType) +
      string.Format("TransactionType: {0}, ", entity.TransactionType) +
      string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
}
```

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>저장소 메트릭 사용 시 발생하는 요금
메트릭용 테이블 엔터티 작성을 위한 쓰기 요청에는 모든 Azure Storage 작업에 적용되는 표준 요금이 부과됩니다.

클라이언트의 메트릭 데이터 읽기 및 삭제 요청 역시 표준 요금이 청구됩니다. 데이터 보존 정책을 구성한 경우에는 Azure Storage에서 이전 메트릭 데이터를 삭제할 때 요금이 부과되지 않습니다. 그러나 분석 데이터를 삭제하는 경우 계정에 대해 삭제 작업 요금이 부과됩니다.

메트릭 테이블에서 사용하는 용량에 대해서도 요금이 청구됩니다. 아래 지침을 참조하여 메트릭 데이터를 저장하는 데 사용될 용량을 예측할 수 있습니다.

* 서비스가 1시간마다 모든 서비스의 모든 API를 사용하는 경우, 서비스 및 API 수준 요약을 모두 사용하도록 설정했다면 메트릭 트랜잭션 테이블에 시간당 약 148KB의 데이터가 저장됩니다.
* 서비스가 1시간마다 모든 서비스의 모든 API를 사용하는 경우, 서비스 수준 요약만 사용하도록 설정했다면 메트릭 트랜잭션 테이블에 시간당 약 12KB의 데이터가 저장됩니다.
* 사용자가 로그를 옵트인(opt in)한 경우 Blob용 용량 테이블에는 매일 2개 행이 추가됩니다. 따라서 이 테이블의 크기가 매일 약 300바이트씩 증가합니다.

## <a name="next-steps"></a>다음 단계
[저장소 로깅 사용 및 로그 데이터 액세스](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data)
