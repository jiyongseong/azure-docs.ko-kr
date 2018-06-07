---
title: Azure Data Factory에서 웹 작업 | Microsoft Docs
description: Data Factory에서 지원하는 제어 흐름 작업 중 하나인 웹 작업을 사용하여 파이프라인에서 REST 끝점을 호출하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: shlo
ms.openlocfilehash: 58faed48f5031b26f1340f3766fdd8bdc6bd2ccb
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32770760"
---
# <a name="web-activity-in-azure-data-factory"></a>Azure Data Factory에서 웹 작업
웹 작업은 Data Factory 파이프라인에서 사용자 지정 REST 끝점을 호출하는 데 사용할 수 있습니다. 작업에서 사용하고 액세스하도록 데이터 집합 및 연결된 서비스를 전달할 수 있습니다. 

> [!NOTE]
> 이 문서는 현재 미리 보기 상태인 Data Factory 버전 2에 적용됩니다. 일반 공급(GA)되는 Data Factory 버전 1 서비스를 사용하는 경우 [Data Factory 버전 1 설명서](v1/data-factory-introduction.md)를 참조하세요.

## <a name="syntax"></a>구문

```json
{  
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{  
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{  
         "Content-Type":"application/json"
      },
      "authentication":{  
         "type":"ClientCertificate",  
         "pfx":"****",
         "password":"****"
      },
      "datasets":[  
         {  
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{  
               ...
            }
         }
      ],
      "linkedServices":[  
         {  
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>형식 속성

자산 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
이름 | 웹 작업의 이름입니다. | 문자열 | 예
형식 | **WebActivity**로 설정해야 합니다. | 문자열 | 예
메서드 | 대상 끝점에 대한 Rest API 메서드입니다. | 문자열입니다. <br/><br/>지원되는 형식: "GET", "POST", "PUT" | 예
URL | 대상 끝점 및 경로입니다. | 문자열(또는 resultType 문자열이 있는 식). 활동이 끝점에서 응답을 수신하지 않는 경우 오류가 발생하여 1분에 시간이 초과됩니다. | 예
headers | 요청에 전송되는 헤더입니다. 예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 문자열(또는 resultType 문자열이 있는 식) | 예, Content-Type 헤더가 필요합니다. `"headers":{ "Content-Type":"application/json"}`
본문 | 끝점에 전송된 페이로드를 나타냅니다. POST/PUT 메서드에 필요합니다.  | 문자열(또는 resultType 문자열이 있는 식). <br/><br/>[요청 페이로드 스키마](#request-payload-schema) 섹션에서 요청 페이로드의 스키마를 참조하세요. | 아니오
인증 | 끝점을 호출하는 데 사용되는 인증 방법입니다. 지원되는 형식은 "Basic" 또는 "ClientCertificate"입니다. 자세한 내용은 [인증](#authentication) 섹션을 참조하세요. 인증이 필요 없는 경우 이 속성을 제외합니다. | 문자열(또는 resultType 문자열이 있는 식) | 아니오
데이터 집합 | 끝점에 전달되는 데이터 집합의 목록입니다. | 데이터 집합 참조의 배열입니다. 빈 배열일 수 있습니다. | 예
linkedServices | 끝점에 전달되는 연결된 서비스 목록입니다. | 연결된 서비스 참조의 배열입니다. 빈 배열일 수 있습니다. | 예

> [!NOTE]
> 웹 작업이 호출하는 REST 끝점은 JSON 형식의 응답을 반환해야 합니다. 활동이 끝점에서 응답을 수신하지 않는 경우 오류가 발생하여 1분에 시간이 초과됩니다.

다음 표에서는 JSON 콘텐츠에 대한 요구 사항을 보여 줍니다.

| 값 형식 | 요청 본문 | 응답 본문 |
|---|---|---|
|JSON 개체 | 지원됨 | 지원됨 |
|JSON 배열 | 지원됨 <br/>(현재 JSON 배열은 버그로 인해 작동하지 않습니다. 수정이 진행 중입니다.) | 지원되지 않음 |
| JSON 값 | 지원됨 | 지원되지 않음 |
| 비-JSON 형식 | 지원되지 않음 | 지원되지 않음 |
||||

## <a name="authentication"></a>인증

### <a name="none"></a>없음
인증이 필요 없는 경우 "authentication" 속성을 포함하지 않습니다.

### <a name="basic"></a>Basic
기본 인증에 사용할 사용자 이름 및 암호를 지정합니다. 

```json
"authentication":{  
   "type":"Basic,
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>클라이언트 인증서
PFX 파일의 base64로 인코딩된 콘텐츠 및 암호를 지정합니다. 

```json
"authentication":{  
   "type":"ClientCertificate",
   "pfx":"****",   
   "password":"****"
}
```
## <a name="request-payload-schema"></a>요청 페이로드 스키마
POST/PUT 메서드를 사용하는 경우 body 속성은 끝점에 전송되는 페이로드를 나타냅니다. 연결된 서비스 및 데이터 집합을 페이로드의 일부로 전달할 수 있습니다. 페이로드 스키마는 다음과 같습니다. 

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
} 
```

## <a name="example"></a>예
이 예제에서 파이프라인의 웹 작업은 REST 끝점을 호출하고 Azure SQL 연결된 서비스 및 Azure SQL 데이터 집합을 끝점에 전달합니다. REST 끝점은 Azure SQL 연결 문자열을 사용하여 Azure SQL Server에 연결하고 SQL Server의 인스턴스 이름을 반환합니다. 

### <a name="pipeline-definition"></a>파이프라인 정의

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>파이프라인 매개 변수 값

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>웹 서비스 끝점 코드

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요. 

- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
