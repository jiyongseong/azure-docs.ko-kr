---
title: REST를 사용하여 Azure Media Services 원격 분석 구성 | Microsoft Docs
description: 이 문서는 REST API를 사용하여 Azure Media Services 원격 분석을 사용하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: e1a314fb-cc05-4a82-a41b-d1c9888aab09
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: ceb2eafdb3df0d24a98d0d3b4afc7d1d9424b4de
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="configuring-azure-media-services-telemetry-with-rest"></a>REST를 사용하여 Azure Media Services 원격 분석 구성

이 항목은 REST API를 사용하여 AMS(Azure Media Services) 원격 분석을 구성할 때 수행할 수 있는 일반적인 단계를 설명합니다. 

>[!NOTE]
>AMS 원격 분석이 무엇인지, 어떻게 사용하는지에 대한 자세한 설명을 보려면 [개요](media-services-telemetry-overview.md) 항목을 참조하세요.

이 항목에서 설명하는 단계는 다음과 같습니다.

- 저장소 계정을 Media Services 계정에 연결
- 알림 끝점 가져오기
- 모니터링을 위한 알림 끝점 만들기 

    알림 엔드포인트를 만들려면 EndPointType을 AzureTable(2)로 설정하고 endPontAddress를 저장소 테이블(예: https://telemetryvalidationstore.table.core.windows.net/))로 설정합니다.
  
- 모니터링 구성 가져오기

    모니터링할 서비스에 대한 모니터링 구성 설정을 만듭니다. 한 개 이하의 모니터링 구성 설정이 허용됩니다. 

- 모니터링 구성 추가


 
## <a name="get-the-storage-account-associated-with-a-media-services-account"></a>저장소 계정을 Media Services 계정에 연결

### <a name="request"></a>요청

    GET https://wamsbnp1clus001rest-hs.cloudapp.net/api/StorageAccounts HTTP/1.1
    x-ms-version: 2.13
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept: application/json; odata=verbose
    Authorization: (redacted)
    Host: wamsbnp1clus001rest-hs.cloudapp.net
    Response
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 370
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 8206e222-2a59-482c-a6a9-de6b8bda57fb
    x-ms-request-id: 8206e222-2a59-482c-a6a9-de6b8bda57fb
    X-Content-Type-Options: nosniff
    DataServiceVersion: 2.0;
    access-control-expose-headers: request-id, x-ms-request-id
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 02 Dec 2015 05:10:40 GMT
    
    {"d":{"results":[{"__metadata":{"id":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/StorageAccounts('telemetryvalidationstore')","uri":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/StorageAccounts('telemetryvalidationstore')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.StorageAccount"},"Name":"telemetryvalidationstore","IsDefault":true,"BytesUsed":null}]}}

## <a name="get-the-notification-endpoints"></a>알림 끝점 가져오기

### <a name="request"></a>요청

    GET https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints HTTP/1.1
    x-ms-version: 2.13
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept: application/json; odata=verbose
    Authorization: (redacted)
    Host: wamsbnp1clus001rest-hs.cloudapp.net
    
### <a name="response"></a>response
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 20
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: c68de2b3-0be1-4823-b622-6ca6f94a96b5
    x-ms-request-id: c68de2b3-0be1-4823-b622-6ca6f94a96b5
    X-Content-Type-Options: nosniff
    DataServiceVersion: 2.0;
    access-control-expose-headers: request-id, x-ms-request-id
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 02 Dec 2015 05:10:40 GMT
    
    {  
        "d":{  
            "results":[]
        }
    }
 
## <a name="create-a-notification-endpoint-for-monitoring"></a>모니터링을 위한 알림 끝점 만들기

### <a name="request"></a>요청

    POST https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints HTTP/1.1
    x-ms-version: 2.13
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept: application/json; odata=verbose
    Authorization: (redacted)
    Content-Type: application/json; charset=utf-8
    Host: wamsbnp1clus001rest-hs.cloudapp.net
    Content-Length: 115
    
    {  
        "Name":"monitoring",
        "EndPointAddress":"https://telemetryvalidationstore.table.core.windows.net/",
        "EndPointType":2
    }

>[!NOTE]
>“https://telemetryvalidationstore.table.core.windows.net” 값을 사용자의 저장소 계정으로 변경해야 합니다.

### <a name="response"></a>response

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 578
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')
    Server: Microsoft-IIS/8.5
    request-id: e8fa5a60-7d8b-4b00-a7ee-9b0f162fe0a9
    x-ms-request-id: e8fa5a60-7d8b-4b00-a7ee-9b0f162fe0a9
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    access-control-expose-headers: request-id, x-ms-request-id
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 02 Dec 2015 05:10:42 GMT
    
    {"d":{"__metadata":{"id":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')","uri":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.NotificationEndPoint"},"Id":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4","Name":"monitoring","Created":"\/Date(1449033042667)\/","EndPointAddress":"https://telemetryvalidationstore.table.core.windows.net/","EndPointType":2}}
 
## <a name="get-the-monitoring-configurations"></a>모니터링 구성 가져오기

### <a name="request"></a>요청

    GET https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations HTTP/1.1
    x-ms-version: 2.13
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept: application/json; odata=verbose
    Authorization: (redacted)
    Host: wamsbnp1clus001rest-hs.cloudapp.net

### <a name="response"></a>response
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 20
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 00a3ee37-bb19-4fca-b5c7-a92b629d4416
    x-ms-request-id: 00a3ee37-bb19-4fca-b5c7-a92b629d4416
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    access-control-expose-headers: request-id, x-ms-request-id
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 02 Dec 2015 05:10:42 GMT
    
    {"d":{"results":[]}}

## <a name="add-a-monitoring-configuration"></a>모니터링 구성 추가

### <a name="request"></a>요청

    POST https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations HTTP/1.1
    x-ms-version: 2.13
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept: application/json; odata=verbose
    Authorization: (redacted)
    Content-Type: application/json; charset=utf-8
    Host: wamsbnp1clus001rest-hs.cloudapp.net
    Content-Length: 133
    
    {  
       "NotificationEndPointId":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4",
       "Settings":[  
          {  
         "Component":"Channel",
         "Level":"Normal"
          }
       ]
    }

### <a name="response"></a>response

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 825
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')
    Server: Microsoft-IIS/8.5
    request-id: daede9cb-8684-41b0-a921-a3af66430cbe
    x-ms-request-id: daede9cb-8684-41b0-a921-a3af66430cbe
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    access-control-expose-headers: request-id, x-ms-request-id
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 02 Dec 2015 05:10:43 GMT
    
    {"d":{"__metadata":{"id":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')","uri":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.MonitoringConfiguration"},"Id":"nb:mcid:UUID:1a8931ae-799f-45fd-8aeb-9641740295c2","NotificationEndPointId":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4","Created":"2015-12-02T05:10:43.7680396Z","LastModified":"2015-12-02T05:10:43.7680396Z","Settings":{"__metadata":{"type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.ComponentMonitoringSettings)"},"results":[{"Component":"Channel","Level":"Normal"},{"Component":"StreamingEndpoint","Level":"Disabled"}]}}}

## <a name="stop-telemetry"></a>원격 분석 중지

### <a name="request"></a>요청

    DELETE https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')
    x-ms-version: 2.13
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept: application/json; odata=verbose
    Authorization: (redacted)
    Content-Type: application/json; charset=utf-8
    Host: wamsbnp1clus001rest-hs.cloudapp.net

## <a name="consuming-telemetry-information"></a>이

원격 분석 정보 사용에 대한 자세한 내용은 [이](media-services-telemetry-overview.md) 항목을 참조하세요.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
