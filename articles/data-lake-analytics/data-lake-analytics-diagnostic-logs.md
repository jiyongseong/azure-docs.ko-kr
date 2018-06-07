---
title: Azure Data Lake Analytics에 대한 진단 로그 보기 | Microsoft Docs
description: 'Azure Data Lake Analytics에 대한 진단 로그를 설정하고 액세스하는 방법을 이해합니다. '
services: data-lake-analytics
documentationcenter: ''
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/12/2018
ms.openlocfilehash: efec1e00e9c2da519028f7e6ff094a0f0876df79
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics에 대한 진단 로그에 액세스

진단 로깅을 사용하면 데이터 액세스 감사 내역을 수집할 수 있습니다. 이러한 로그는 다음과 같은 정보를 제공합니다.

* 데이터에 액세스하는 사용자의 목록.
* 데이터가 액세스되는 빈도.
* 계정에 저장된 데이터의 양.

## <a name="enable-logging"></a>로깅 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. Data Lake Analytics 계정을 열고 __모니터링__ 섹션에서 **진단 로그**를 선택합니다. 다음으로, __진단 상태 켜기__를 선택합니다.

    ![진단을 켜서 감사 및 요청 로그 수집](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. __진단 설정__에서 이 로깅 구성에 __이름__을 입력하고 로깅 옵션을 선택합니다.

    ![진단을 켜서 감사 및 요청 로그 수집](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "진단 로그 활성화")

   * 세 가지 방법으로 데이터를 저장/처리하도록 선택할 수 있습니다.

     * Azure Storage 계정에 로그를 저장하려면 __저장소 계정에 보관__을 선택합니다. 데이터를 보관하려는 경우 이 옵션을 사용합니다. 이 옵션을 선택하는 경우 Azure Storage 계정을 제공하여 로그를 저장해야 합니다.

     * Azure Event Hub에 로그 데이터를 스트리밍하려면 **이벤트 허브로 스트리밍**을 선택합니다. 들어오는 로그를 실시간으로 분석하는 다운스트림 처리 파이프라인을 사용하는 경우 이 옵션을 사용합니다. 이 옵션을 선택하는 경우 사용하려는 Azure 이벤트 허브에 대한 세부 정보를 제공해야 합니다.

     * __Log Analytics로 보내기__를 선택하여 데이터를 Log Analytics 서비스로 보냅니다. Log Analytics를 사용하여 로그를 수집하고 분석하려는 경우 이 옵션을 사용합니다.
   * 감사 로그 또는 요청 로그를 가져올지, 혹은 둘 모두를 가져올지를 지정합니다.  요청 로그는 모든 API 요청을 캡처합니다. 감사 로그는 해당 API 요청에 의해 트리거되는 모든 작업을 기록합니다.

   * __저장소 계정에 보관__의 경우 데이터를 보관할 일 수를 지정합니다.

   * __Save__를 클릭합니다.

        > [!NOTE]
        > __저장__ 단추를 클릭하기 전에 __저장소 계정에 보관__, __이벤트 허브로 스트리밍__ 또는 __Log Analytics로 보내기__를 선택해야 합니다.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>로그 데이터를 포함하는 Azure Storage 계정 사용

1. 로깅 데이터를 유지하는 Blob 컨테이너를 표시하려면 로깅을 위해 Data Lake Analytics에 사용되는 Azure Storage 계정을 열고 __Blob__을 클릭합니다.

   * **insights-logs-audit** 컨테이너는 감사 로그를 포함합니다.
   * **insights-logs-requests** 컨테이너는 요청 로그를 포함합니다.

2. 이러한 컨테이너 내에서 로그는 다음 파일 구조로 저장됩니다.

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > `##` 항목은 로그가 생성된 연도, 월, 일 및 시간을 포함합니다. Data Lake Analytics는 1시간마다 하나의 파일을 만들므로 `m=`은(는) 항상 `00`의 값을 포함합니다.

    예를 들어, 감사 로그에 대한 전체 경로는 다음과 같을 수 있습니다.

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    마찬가지로 요청 로그에 대한 전체 경로는 다음과 같을 수 있습니다.

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>로그 구조

감사 로그 및 요청 로그는 구조화된 JSON 형식입니다.

### <a name="request-logs"></a>요청 로그

다음은 JSON 형식인 요청 로그의 샘플 항목입니다. 각 Blob는 **레코드** 라는 하나의 루트 개체를 포함하며 여기에는 로그 개체의 배열이 포함됩니다.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>요청 로그 스키마

| Name | type | 설명 |
| --- | --- | --- |
| 실시간 |문자열 |로그의 타임스탬프(UTC) |
| ResourceId |문자열 |작업이 수행되는 리소스의 식별자 |
| 카테고리 |문자열 |로그 범주 예: **Requests** |
| operationName |문자열 |기록된 작업의 이름 예를 들어 GetAggregatedJobHistory |
| resultType |문자열 |작업의 상태, 예를 들어 200 |
| callerIpAddress |문자열 |요청한 클라이언트의 IP 주소 |
| CorrelationId |문자열 |로그의 식별자입니다. 이 값을 사용하여 관련된 로그 항목의 집합을 그룹화할 수 있습니다. |
| ID |Object |로그를 생성하는 ID |
| properties |JSON |자세한 내용은 다음 섹션(요청 로그 속성 스키마)을 참조하세요. |

#### <a name="request-log-properties-schema"></a>요청 로그 속성 스키마

| Name | type | 설명 |
| --- | --- | --- |
| HttpMethod |문자열 |작업에 사용된 HTTP 메서드 예를 들어 GET |
| path |문자열 |작업이 수행된 경로 |
| RequestContentLength |int |HTTP 요청의 콘텐츠 길이 |
| ClientRequestId |문자열 |이 요청을 고유하게 식별하는 식별자 |
| StartTime |문자열 |서버가 요청을 받은 시간 |
| EndTime |문자열 |서버가 응답을 전송한 시간 |

### <a name="audit-logs"></a>감사 로그

다음은 JSON 형식인 감사 로그의 샘플 항목입니다. 각 Blob는 **레코드** 라는 하나의 루트 개체를 포함하며 여기에는 로그 개체의 배열이 포함됩니다.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>감사 로그 스키마

| Name | type | 설명 |
| --- | --- | --- |
| 실시간 |문자열 |로그의 타임스탬프(UTC) |
| ResourceId |문자열 |작업이 수행되는 리소스의 식별자 |
| 카테고리 |문자열 |로그 범주 예: **Audit**. |
| operationName |문자열 |기록된 작업의 이름 예를 들어 JobSubmitted |
| resultType |문자열 |작업 상태(operationName)에 대한 하위 상태입니다. |
| resultSignature |문자열 |작업 상태(operationName)에 추가 세부 정보입니다. |
| ID |문자열 |작업을 요청한 사용자입니다. 예: susan@contoso.com |
| properties |JSON |자세한 내용은 다음 섹션(감사 로그 속성 스키마)을 참조하세요. |

> [!NOTE]
> **resultType** 및 **resultSignature**는 작업의 결과에 대한 정보를 제공하고 작업이 완료되었을 경우에 값을 포함합니다. 예를 들어 **operationName**이 **JobStarted** 또는 **JobEnded**의 값을 포함할 때 값을 포함합니다.
>
>

#### <a name="audit-log-properties-schema"></a>감사 로그 속성 스키마

| Name | type | 설명 |
| --- | --- | --- |
| JobId |문자열 |작업에 할당된 ID |
| JobName |문자열 |작업에 대해 제공된 이름 |
| JobRunTime |문자열 |작업을 처리하는 데 사용된 런타임 |
| SubmitTime |문자열 |작업이 제출된 시간(UTC) |
| StartTime |문자열 |제출 후(UTC) 작업이 실행을 시작한 시간 |
| EndTime |문자열 |작업이 종료된 시간 |
| 병렬 처리 |문자열 |제출하는 동안 이 작업에 대해 요청된 Data Lake Analytics 단위의 수 |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime** 및 **Parallelism**은 작업에 대한 정보를 제공합니다. 해당 작업이 시작 또는 완료되는 경우 이러한 항목만 값을 포함합니다. 예를 들어 **operationName**이 **JobSubmitted** 값을 가진 후 **SubmitTime**은 값을 포함합니다.

## <a name="process-the-log-data"></a>로그 데이터 처리

Azure Data Lake Analytics에서는 로그 데이터를 처리하고 분석하는 방법에 대한 샘플을 제공합니다. [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)에서 샘플을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Data Lake Analytics 개요](data-lake-analytics-overview.md)
