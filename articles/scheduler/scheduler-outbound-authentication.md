---
title: "스케줄러 아웃바운드 인증"
description: "스케줄러 아웃바운드 인증"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2016
ms.author: deli
ms.openlocfilehash: e345b2e22daae5b24c23645f7d2636f66df630ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="scheduler-outbound-authentication"></a>스케줄러 아웃바운드 인증
인증을 요구하는 서비스를 호출하기 위해 스케줄러 작업이 필요할 수 있습니다. 이를 통해, 호출된 서비스가 스케줄러 작업이 리소스에 액세스 가능한지 여부를 결정할 수 있습니다. 이러한 서비스 중 일부에는 다른 Azure 서비스, Salesforce.com, Facebook 및 보안 사용자 지정 웹사이트가 포함됩니다.

## <a name="adding-and-removing-authentication"></a>인증 추가 및 제거
스케줄러 작업에 간단하게 인증을 추가할 수 있습니다. 즉 작업을 만들거나 업데이트할 때 JSON 자식 요소 `authentication`을 `request` 요소에 추가합니다. `authentication` 개체의 일부로 PUT, PATCH 또는 POST 요청에서 스케줄러 서비스에 전달되는 암호는 응답에서 절대 반환되지 않습니다. 응답에서 암호 정보는 null로 설정되거나, 인증된 엔터티를 나타내는 공용 토큰을 갖을 수 있습니다.

인증을 제거하려면 작업을 명시적으로 PUT 또는 PATCH하여 `authentication` 개체를 null로 설정합니다. 응답에는 인증 속성이 하나도 표시되지 않습니다.

현재 지원되는 인증 유형은 `ClientCertificate`모델(SSL/TLS 클라이언트 인증서를 사용할 경우), `Basic`(기본 인증의 경우), `ActiveDirectoryOAuth`(Active Directory OAuth 인증용)뿐입니다.

## <a name="request-body-for-clientcertificate-authentication"></a>ClientCertificate 인증에 대한 요청 본문
`ClientCertificate` 모델을 사용하여 인증을 추가할 때는 다음 추가 요소를 요청 본문에 추가합니다.  

| 요소 | 설명 |
|:--- |:--- |
| *인증(부모 요소)* |SSL 클라이언트 인증서를 사용하기 위한 인증 개체 입니다. |
| *type* |필수입니다. 인증 유형입니다. SSL 클라이언트 인증서의 경우 이 값은 `ClientCertificate`입니다. |
| *pfx* |필수입니다. PFX 파일의 Base64 인코딩 콘텐츠입니다. |
| *암호* |필수입니다. PFX 파일에 액세스하기 위한 암호입니다. |

## <a name="response-body-for-clientcertificate-authentication"></a>ClientCertificate 인증에 대한 응답 본문
인증 정보와 함께 요청을 보내면 응답에는 다음 인증 관련 요소가 포함됩니다.

| 요소 | 설명 |
|:--- |:--- |
| *인증(부모 요소)* |SSL 클라이언트 인증서를 사용하기 위한 인증 개체 입니다. |
| *type* |인증 유형입니다. SSL 클라이언트 인증서의 경우 이 값은 `ClientCertificate`입니다. |
| *certificateThumbprint* |인증서의 지문입니다. |
| *certificateSubjectName* |인증서의 주체 고유 이름입니다. |
| *certificateExpiration* |인증서의 만료일입니다. |

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>ClientCertificate 인증에 대한 샘플 REST 요청
```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>ClientCertificate 인증에 대한 샘플 REST 응답
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="request-body-for-basic-authentication"></a>기본 인증의 요청 본문
`Basic` 모델을 사용하여 인증을 추가할 때는 다음 추가 요소를 요청 본문에 추가합니다.

| 요소 | 설명 |
|:--- |:--- |
| *인증(부모 요소)* |기본 인증을 사용 하기 위한 인증 개체입니다. |
| *type* |필수입니다. 인증 유형입니다. 기본 인증의 경우 이 값은 `Basic`입니다. |
| *사용자 이름* |필수입니다. 인증하기 위한 사용자 이름입니다. |
| *암호* |필수입니다. 인증하기 위한 암호입니다. |

## <a name="response-body-for-basic-authentication"></a>기본 인증의 응답 본문
인증 정보와 함께 요청을 보내면 응답에는 다음 인증 관련 요소가 포함됩니다.

| 요소 | 설명 |
|:--- |:--- |
| *인증(부모 요소)* |기본 인증을 사용 하기 위한 인증 개체입니다. |
| *type* |인증 유형입니다. 기본 인증의 경우 이 값은 `Basic`입니다. |
| *사용자 이름* |인증된 사용자 이름입니다. |

## <a name="sample-rest-request-for-basic-authentication"></a>기본 인증에 대한 샘플 REST 요청
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>기본 인증에 대한 샘플 REST 응답
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 인증의 요청 본문
`ActiveDirectoryOAuth` 모델을 사용하여 인증을 추가할 때는 다음 추가 요소를 요청 본문에 추가합니다.

| 요소 | 설명 |
|:--- |:--- |
| *인증(부모 요소)* |ActiveDirectoryOAuth 인증을 사용하기 위한 인증 개체입니다. |
| *type* |필수입니다. 인증 유형입니다. ActiveDirectoryOAuth 인증의 경우 이 값은 `ActiveDirectoryOAuth`입니다. |
| *테넌트* |필수입니다. Azure AD 테넌트의 테넌트 식별자입니다. |
| *대상* |필수입니다. 이는 https://management.core.windows.net/에 설정됩니다. |
| *clientId* |필수입니다. Azure AD 응용 프로그램의 클라이언트 ID를 제공합니다. |
| *암호* |필수입니다. 토큰을 요청하는 클라이언트의 암호입니다. |

### <a name="determining-your-tenant-identifier"></a>테넌트 식별자 확인
Azure PowerShell에서 `Get-AzureAccount` 를 실행하여 Azure AD 테넌트의 테넌트 식별자를 확인할 수 있습니다.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 인증의 응답 본문
인증 정보와 함께 요청을 보내면 응답에는 다음 인증 관련 요소가 포함됩니다.

| 요소 | 설명 |
|:--- |:--- |
| *인증(부모 요소)* |ActiveDirectoryOAuth 인증을 사용하기 위한 인증 개체입니다. |
| *type* |인증 유형입니다. ActiveDirectoryOAuth 인증의 경우 이 값은 `ActiveDirectoryOAuth`입니다. |
| *테넌트* |Azure AD 테넌트의 테넌트 식별자입니다. |
| *대상* |이는 https://management.core.windows.net/에 설정됩니다. |
| *clientId* |Azure AD 응용 프로그램의 클라이언트 ID입니다. |

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 인증에 대한 샘플 REST 요청
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth 인증에 대한 샘플 REST 응답
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>참고 항목
 [스케줄러란?](scheduler-intro.md)

 [Azure 스케줄러 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [Azure 포털에서 스케줄러 사용 시작](scheduler-get-started-portal.md)

 [Azure 스케줄러의 버전 및 요금 청구](scheduler-plans-billing.md)

 [Azure 스케줄러 REST API 참조](https://msdn.microsoft.com/library/mt629143)

 [Azure 스케줄러 PowerShell cmdlet 참조](scheduler-powershell-reference.md)

 [Azure 스케줄러 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [Azure 스케줄러 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

