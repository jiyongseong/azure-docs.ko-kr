---
title: 사용 API 관련 Faq | Microsoft Docs
description: Azure 스택 미터, Azure 사용량 API, 사용 시간 및 보고 된 시간, 오류 코드를 있는 비교 목록입니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: f0df9b02e7672faec3a1d94997c9b27ffca275e5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>질문과 대답 Azure 스택 사용량 API
이 문서 Azure 스택 사용량 API에 대 한 몇 가지 자주 묻는 질문에 대답 합니다.

## <a name="what-meter-ids-can-i-see"></a>어떤 미터 Id를 볼 수 있습니까?
다음 리소스 공급자에 대 한 사용 현황 보고 됩니다.

| **리소스 공급자** | **측정기 ID** | **측정 이름** | **단위** | **추가 정보** |
| --- | --- | --- | --- | --- |
| **네트워크** |F271A8A388C44D93956A063E1D2FA80B |고정 IP 주소 사용 |IP 주소| 사용 되는 수의 IP 주소입니다. 단위는 일일 사용량 API를 호출 하면 요금 계산이 시간 수를 곱한 IP 주소를 반환 합니다. |
| |9E2739BA86744796B465F64674B822BA |동적 IP 주소 사용 |IP 주소| 사용 되는 수의 IP 주소입니다. 단위는 일일 사용량 API를 호출 하면 요금 계산이 시간 수를 곱한 IP 주소를 반환 합니다. |
| **Storage** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*시간 |테이블에서 사용 하는 총 용량입니다. |
| |B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*시간 |페이지 blob에서 소비 된을 총 수입니다. |
| |B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*시간 |큐에서 사용 하는 총 용량입니다. |
| |09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*시간 |블록 blob에서 소비 된을 총 수입니다. |
| |B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |000's 10에서 요청 수 |테이블 서비스 요청 (10, 000s)입니다. |
| |50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Gb에서는 송 데이터 |테이블 서비스 데이터 유입 gb에서입니다. |
| |1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Gb에서 송신 |테이블 서비스 데이터 유출을 gb |
| |43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |000's 10에서 요청 수 |서비스 요청 (10, 000s)에서 blob 수입니다. |
| |9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Gb에서는 송 데이터 |서비스 데이터 유입 gb에서 blob 수입니다. |
| |3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Gb에서 송신 |Gb에서 blob 서비스의 데이터 송신 합니다. |
| |EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |000's 10에서 요청 수 |서비스 요청 (10, 000s)에서 큐에 넣습니다. |
| |E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Gb에서는 송 데이터 |큐 서비스 데이터 유입 gb에서입니다. |
| |DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Gb에서 송신 |큐 서비스 데이터 유출을 gb |
| **Sql RP**            | CBCFEF9A-B91F-4597-A4D3-01FE334BED82 | DatabaseSizeHourSqlMeter   | MB\*시간   | 생성 시 총 DB 용량입니다. 단위는 일일 사용량 API를 호출 하면 요금 계산이 시간 수를 곱한 MB를 반환 합니다. |
| **MySql RP**          | E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3 | DatabaseSizeHourMySqlMeter | MB\*시간    | 생성 시 총 DB 용량입니다. 단위는 일일 사용량 API를 호출 하면 요금 계산이 시간 수를 곱한 MB를 반환 합니다. |
| **Compute** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |기본 VM 크기 시간 |가상 코어 시간 | 가상 코어 수가 VM 실행 시간에 곱합니다. |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows VM 크기 시간 |가상 코어 시간 | 가상 코어 수가 VM 실행 시간에 곱합니다. |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |VM 크기 시간 |VM 시간 |자료와 Windows VM을 캡처합니다. 코어에 대 한 조정 되지 않습니다. |
| **Key Vault** |EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |주요 자격 증명 모음 트랜잭션 | 000's 10에서 요청 수| 주요 자격 증명 모음 데이터 평면에서 수신 하는 REST API 요청 수입니다. |
| |2C354225-B2FE-42E5-AD89-14F0EA302C87 |고급 키 트랜잭션 | 10, 000 개 트랜잭션|     RSA 3 K/4k, ECC 키 트랜잭션이 없습니다. (미리 보기)입니다. |
| **App service** | 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA | App Service | 가상 코어 시간 | 앱 서비스를 실행 하는 데 대 가상 코어 수입니다. 참고: Microsoft Azure 스택 앱 서비스 요금을 청구 하이 미터는 사용 합니다. 클라우드 서비스 공급자가 테 넌 트에 대 한 사용을 계산 하기는 다른 앱 서비스 미터 (아래 참조)를 사용할 수 있습니다. |
|  | 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE | 함수 요청 | 10 개 요청 | 10 개의 실행) (당 요청 된 실행의 총 수입니다. 함수는 이벤트에 대 한 응답으로 실행 되거나 바인딩을 사용 하 여 트리거될 때마다 실행으로 계산 됩니다. |
|  | D1D04836-075C-4F27-BF65-0A1130EC60ED | Compute 함수- | GB-s | 리소스 소비량 gigabyte 초 (g B/s) 단위로 지정 합니다. **리소스 사용을 관찰** gb에서 평균 메모리 크기 (밀리초) 기능을 실행 하는 데 걸리는 시간을 곱하여 계산 됩니다. 함수에 의해 사용 되는 메모리는 실행 시간을 반올림 하 여 계산 된 최대 메모리 크기의 1, 536 MB 최대 가장 가까운 128MB까지 반올림 하 여 측정 됩니다 최대 가장 가까운 1 밀리초입니다. 최소 실행 시간 및 단일 함수 실행에 대 한 메모리 100ms 및 128mb 각각입니다. |
|  | 957E9F36-2C14-45A1-B6A1-1723EF71A01D | 공유 앱 서비스 시간 | 1시간 | 당 분할 앱 서비스 계획의 시간 사용량입니다. 계획 당 응용 프로그램 별로 요금이 부과 되며 합니다. |
|  | 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9 | 무료 앱 서비스 시간 | 1시간 | 당 무료 앱 서비스 계획의 시간 사용량입니다. 계획 당 응용 프로그램 별로 요금이 부과 되며 합니다. |
|  | 88039D51-A206-3A89-E9DE-C5117E2D10A6 | 표준 소량 앱 서비스 시간 | 1시간 | 크기와 인스턴스 수에 따라 계산 됩니다. |
|  | 83A2A13E-4788-78DD-5D55-2831B68ED825 | 표준 중간 앱 서비스 시간 | 1시간 | 크기와 인스턴스 수에 따라 계산 됩니다. |
|  | 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6 | 표준 대량 앱 서비스 시간 | 1시간 | 크기와 인스턴스 수에 따라 계산 됩니다. |
|  | *사용자 지정 작업자 계층* | 사용자 지정 작업자 계층 | 시간 | 결정적 미터 ID SKU 및 사용자 지정 작업자 계층 이름에 따라 생성 됩니다. 이 미터 ID는 각 사용자 지정 작업자 계층에 대해 고유 합니다. |
|  | 264ACB47-AD38-47F8-ADD3-47F01DC4F473 | SNI SSL | SNI SSL 바인딩 당 | 앱 서비스는 두 가지 유형의 SSL 연결: 서버 이름 SNI (표시) SSL 연결 및 IP 주소 SSL 연결 합니다. IP 기반 SSL은 모든 브라우저에서 작동하는 반면 SNI 기반 SSL은 최신 브라우저에서 작동합니다. |
|  | 60B42D72-DC1C-472C-9895-6C516277EDB4 | IP SSL | 각 IP 기반 SSL 바인딩을 | 앱 서비스는 두 가지 유형의 SSL 연결: 서버 이름 SNI (표시) SSL 연결 및 IP 주소 SSL 연결 합니다. IP 기반 SSL은 모든 브라우저에서 작동하는 반면 SNI 기반 SSL은 최신 브라우저에서 작동합니다. |
|  | 73215A6C-FA54-4284-B9C1-7E8EC871CC5B | 웹 프로세스 |  | 시간당 활성 사이트당 계산 됩니다. |
|  | 5887D39B-0253-4E12-83C7-03E1A93DFFD9 | 외부 송신 대역폭 | GB | 총 수신 요청 응답 바이트 + 총 나가는 요청 바이트 + 들어오는 총 FTP 요청 응답 바이트 + 총 들어오는 웹 요청 응답 바이트를 배포 합니다. |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Azure 스택 사용량 Api와 비교 하는 방법의 [Azure 사용량 API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (현재 공개 미리 보기)에 있습니까?
* 테 넌 트 사용량 API는 한 가지 예외로 Azure API와 일치:는 *showDetails* 플래그 현재 Azure 스택에서 지원 되지 않습니다.
* 공급자 사용량 API는 Azure 스택만 적용 됩니다.
* 현재는 [RateCard API](https://msdn.microsoft.com/library/azure/mt219004.aspx) 즉 Azure에서 사용할 수 있는 Azure 스택에서 사용할 수 없는 합니다.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>사용 시간과 보고 된 시간 사이의 차이 무엇입니까?
사용 현황 데이터 보고서는 두 개의 기본 시간 값을 갖습니다.

* **한 번도 보고**합니다. 사용 이벤트 사용 시스템을 입력 하는 경우 시간
* **사용 시간이**합니다. Azure 스택 리소스를 사용 하는 경우 시간

사용 시간과 보고 된 시간에 대 한 특정 사용 이벤트에 대 한 값에서 차이점을 볼 수 있습니다. 지연 때 모든 환경에 여러 개의 시간 만큼 될 수 있습니다.

현재 쿼리할 수 있습니다만 *보고 된 시간은*합니다.

## <a name="what-do-these-usage-api-error-codes-mean"></a>이러한 사용량 API 오류 코드는 무엇을 의미 합니까?
| **HTTP 상태 코드** | **오류 코드** | **설명** |
| --- | --- | --- |
| 400/잘못 된 요청 |*NoApiVersion* |*api 버전* 쿼리 매개 변수가 누락 되었습니다. |
| 400/잘못 된 요청 |*InvalidProperty* |속성이 누락 되었거나 잘못 된 값입니다. 응답 본문에 오류 코드에 있는 메시지는 누락 된 속성을 식별합니다. |
| 400/잘못 된 요청 |*RequestEndTimeIsInFuture* |에 대 한 값 *reportedendtime의* 시작일이 미래입니다. 나중에 허용 되지 않습니다이 인수에 대 한 합니다. |
| 400/잘못 된 요청 |*SubscriberIdIsNotDirectTenant* |공급자 API 호출에는 구독 ID는 호출자의 유효한 테 넌 트를 사용 했습니다. |
| 400/잘못 된 요청 |*SubscriptionIdMissingInRequest* |호출자의 구독 ID가 없습니다. |
| 400/잘못 된 요청 |*InvalidAggregationGranularity* |잘못 된 집계 세분성 요청 되었습니다. 유효한 값은 일별 및 시간입니다. |
| 503 |*ServiceUnavailable* |서비스 사용량이 호출이 제한 되 고 다시 시도 가능한 오류가 발생 했습니다. |

## <a name="next-steps"></a>다음 단계
[대금 청구 및 차지 백 Azure 스택](azure-stack-billing-and-chargeback.md)

[공급자 리소스 사용량 API](azure-stack-provider-resource-api.md)

[테 넌 트 리소스 사용량 API](azure-stack-tenant-resource-usage-api.md)
