---
title: Azure Data Lake Store에 대한 진단 로그 보기 | Microsoft 문서
description: 'Azure Data Lake Store에 대한 진단 로그를 설정하고 액세스하는 방법을 이해합니다  '
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: ed401b1d68463bc03f7931e80e2bfb18d9449970
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2018
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Azure Data Lake Store에 대한 진단 로그에 액세스
Data Lake Store 계정에 대한 진단 로깅을 사용하는 방법 및 계정에 대해 수집된 로그를 보는 방법에 대해 알아봅니다.

조직에서는 Azure Data Lake Store 계정에 대한 진단 로깅을 사용하도록 설정하여 데이터에 액세스하는 사용자의 목록, 데이터가 액세스되는 빈도, 계정에 저장된 데이터 양과 같은 정보를 제공하는 데이터 액세스 감사 추적을 수집할 수 있습니다. 활성화되면 요청은 최상의 노력을 기준으로 기록됩니다. 서비스 끝점에 대한 요청이 있는 경우에만 요청 및 진단 로그 항목이 만들어집니다.

## <a name="prerequisites"></a>필수 조건
* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure Data Lake Store 계정**. [Azure Portal을 사용하여 Azure Data Lake Store 시작](data-lake-store-get-started-portal.md)에 있는 지침을 따릅니다.

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Data Lake Store 계정에 대한 진단 로깅 사용
1. 새로운 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Data Lake Store 계정을 열고 Data Lake Store 계정 블레이드에서 **진단 로그**를 클릭합니다.
3. **진단 로그** 블레이드에서 **진단 켜기**를 클릭합니다.

    ![진단 로깅 사용](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "진단 로그 사용")

3. **진단** 블레이드에서 진단 로깅을 구성하려면 다음과 같이 변경합니다.
   
    ![진단 로깅 사용](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "진단 로그 사용")
   
   * **이름**으로 진단 로그 구성에 대한 값을 입력합니다.
   * 다양한 방법으로 데이터를 저장/처리하도록 선택할 수 있습니다.
     
        * Azure Storage 계정에 로그를 저장하려면 **저장소 계정에 보관** 옵션을 선택합니다. 나중에 배치로 처리할 데이터를 보관하려는 경우 이 옵션을 사용합니다. 이 옵션을 선택하는 경우 Azure Storage 계정을 제공하여 로그를 저장해야 합니다.
        
        * Azure Event Hub에 로그 데이터를 스트리밍하려면 **이벤트 허브로 스트리밍** 옵션을 선택합니다. 들어오는 로그를 실시간으로 분석하는 다운스트림 처리 파이프라인을 사용하는 경우 대개 이 옵션을 사용합니다. 이 옵션을 선택하는 경우 사용하려는 Azure 이벤트 허브에 대한 세부 정보를 제공해야 합니다.

        * Azure Log Analytics 서비스를 사용하여 생성된 로그 데이터를 분석하려면 **Log Analytics으로 전송** 옵션을 선택합니다. 이 옵션을 선택하는 경우 로그 분석을 수행하는 데 사용하는 Log Analytics 작업 영역에 세부 정보를 제공해야 합니다. Log Analytics를 사용하는 자세한 내용은 [Log Analytics 로그 검색을 사용하여 수집한 데이터 보기 및 분석](../log-analytics/log-analytics-tutorial-viewdata.md)을 참조하세요.
     
   * 감사 로그 또는 요청 로그를 가져올지, 혹은 둘 모두를 가져올지를 지정합니다.
   * 데이터를 유지해야 하는 일 수를 지정합니다. Azure Storage 계정을 사용하여 로그 데이터를 보관하는 경우에만 보존 기능이 적용됩니다.
   * **저장**을 클릭합니다.

진단 설정을 사용하도록 설정했으면 **진단 로그** 탭에서 로그를 볼 수 있습니다.

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Data Lake Store 계정에 대한 진단 로그 보기
두 가지 방법으로 Data Lake Store 계정에 대한 로그 데이터를 볼 수 있습니다.

* Data Lake Store 계정 설정 보기에서
* 데이터가 저장된 Azure Storage 계정에서

### <a name="using-the-data-lake-store-settings-view"></a>Data Lake Store 설정 보기 사용
1. Data Lake Store 계정 **설정** 블레이드에서 **진단 로그**를 클릭합니다.
   
    ![진단 로깅 보기](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "진단 로그 보기") 
2. **진단 로그** 블레이드에서 **감사 로그** 및 **요청 로그**로 분류된 로그가 표시됩니다.
   
   * 요청 로그는 Data Lake Store 계정에 대한 모든 API 요청을 캡처합니다.
   * 감사 로그는 요청 로그와 비슷하지만 Data Lake Store 계정에 수행된 작업의 훨씬 더 세부적인 분석 결과를 제공합니다. 예를 들어, 요청 로그에서 단일 업로드 API 호출은 감사 로그에서 여러 "추가" 작업을 발생시킬 수 있습니다.
3. 로그를 다운로드하려면 각 로그 항목에 대한 **다운로드** 링크를 클릭합니다.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>로그 데이터를 포함하는 Azure Storage 계정에서
1. 로깅을 위한 Data Lake Store와 연결된 Azure Storage 계정 블레이드를 열고 Blob을 클릭합니다. **Blob service** 블레이드는 두 개의 컨테이너를 나열합니다.
   
    ![진단 로깅 보기](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "진단 로그 보기")
   
   * **insights-logs-audit** 컨테이너는 감사 로그를 포함합니다.
   * **insights-logs-requests** 컨테이너는 요청 로그를 포함합니다.
2. 이러한 컨테이너 내에서 로그는 다음 구조로 저장됩니다.
   
    ![진단 로깅 보기](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "진단 로그 보기")
   
    예를 들어, 감사 로그에 대한 전체 경로는 `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    마찬가지로 요청 로그에 대한 전체 경로는 `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`과 같을 수 있습니다.

## <a name="understand-the-structure-of-the-log-data"></a>로그 데이터의 구조 이해
감사 로그 및 요청 로그는 JSON 형식입니다. 이 섹션에서는 요청 로그 및 감사 로그에 대한 JSON의 구조를 살펴봅니다.

### <a name="request-logs"></a>요청 로그
다음은 JSON 형식인 요청 로그의 샘플 항목입니다. 각 Blob는 **레코드** 라는 하나의 루트 개체를 포함하며 여기에는 로그 개체의 배열이 포함됩니다.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>요청 로그 스키마
| Name | type | 설명 |
| --- | --- | --- |
| 실시간 |문자열 |로그의 타임스탬프(UTC) |
| ResourceId |문자열 |작업이 수행되는 리소스의 ID |
| 카테고리 |문자열 |로그 범주 예: **Requests** |
| operationName |문자열 |기록된 작업의 이름 예를 들어 getfilestatus |
| resultType |문자열 |작업의 상태, 예를 들어 200 |
| callerIpAddress |문자열 |요청한 클라이언트의 IP 주소 |
| CorrelationId |문자열 |관련된 로그 항목의 집합을 그룹화하는 데 사용할 수 있는 로그의 ID |
| ID |Object |로그를 생성하는 ID |
| properties |JSON |자세한 내용은 다음을 참조하세요. |

#### <a name="request-log-properties-schema"></a>요청 로그 속성 스키마
| Name | type | 설명 |
| --- | --- | --- |
| HttpMethod |문자열 |작업에 사용된 HTTP 메서드 예를 들어 GET |
| path |문자열 |작업이 수행된 경로 |
| RequestContentLength |int |HTTP 요청의 콘텐츠 길이 |
| ClientRequestId |문자열 |이 요청을 고유하게 식별하는 ID |
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
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "resultSignature": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>감사 로그 스키마
| Name | type | 설명 |
| --- | --- | --- |
| 실시간 |문자열 |로그의 타임스탬프(UTC) |
| ResourceId |문자열 |작업이 수행되는 리소스의 ID |
| 카테고리 |문자열 |로그 범주 예: **Audit**. |
| operationName |문자열 |기록된 작업의 이름 예를 들어 getfilestatus |
| resultType |문자열 |작업의 상태, 예를 들어 200 |
| resultSignature |문자열 |작업에 대한 추가 세부 정보입니다. |
| CorrelationId |문자열 |관련된 로그 항목의 집합을 그룹화하는 데 사용할 수 있는 로그의 ID |
| ID |Object |로그를 생성하는 ID |
| properties |JSON |자세한 내용은 다음을 참조하세요. |

#### <a name="audit-log-properties-schema"></a>감사 로그 속성 스키마
| Name | type | 설명 |
| --- | --- | --- |
| StreamName |문자열 |작업이 수행된 경로 |

## <a name="samples-to-process-the-log-data"></a>로그 데이터를 처리하는 샘플
Azure Data Lake Store에서 Azure Log Analytics로 로그를 보낼 때(Log Analytics를 사용하는 자세한 내용은 [Log Analytics 로그 검색을 사용하여 수집한 데이터 보기 및 분석](../log-analytics/log-analytics-tutorial-viewdata.md) 참조) 다음 쿼리는 시각적 개체 차트와 함께 사용자 표시 이름 목록, 이벤트의 시간 및 이벤트의 시간에 대한 이벤트 수를 포함하는 테이블을 반환합니다. 사용자 GUID 또는 기타 특성을 표시하도록 쉽게 수정할 수 있습니다.

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Store에서는 로그 데이터를 처리하고 분석하는 방법에 대한 샘플을 제공합니다. [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample)에서 샘플을 찾을 수 있습니다. 

## <a name="see-also"></a>참고 항목
* [Azure 데이터 레이크 저장소 개요](data-lake-store-overview.md)
* [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)

