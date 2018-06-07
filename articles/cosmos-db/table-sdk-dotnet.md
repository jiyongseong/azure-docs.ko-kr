---
title: Azure CosmosDB Table API .NET SDK 및 리소스 | Microsoft Docs
description: 릴리스 날짜, 사용 중지 날짜 및 각 버전의 변경 내용을 포함하여 Azure Cosmos DB Table API에 대해 자세히 알아봅니다.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/26/2018
ms.author: rnagpal
ms.openlocfilehash: 7e012e07b8f93554ea44404c611a7bc0eb64a0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET API: 다운로드 및 릴리스 정보
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK 다운로드**|[NuGet](https://aka.ms/acdbtablenuget)|
|**API 설명서**|[.NET API 참조 설명서](https://aka.ms/acdbtableapiref)|
|**빠른 시작**|[Azure Cosmos DB: .NET 및 Table API를 사용하여 앱 빌드](create-table-dotnet.md)|
|**자습서**|[Azure Cosmos DB: .NET의 Table API를 사용하여 개발](tutorial-develop-table-dotnet.md)|
|**현재 지원되는 프레임워크**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> 미리 보기 도중에 Table API 계정을 만든 경우 일반 공급되는 Table API SDK와 작동하는 [새 Table API 계정](create-table-dotnet.md#create-a-database-account)을 만드세요.
>

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* 직접 모드에서 잘못된 형식의 Etag에 대한 유효성 검사를 추가했습니다.
* 게이트웨이 모드에서 LINQ 쿼리 버그를 수정했습니다.
* 이제 동기 API는 SynchronizationContext를 사용하여 스레드 풀에서 실행됩니다.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* TableRequestOptions에 TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism 및 TableQueryContinuationTokenLimitInKb 추가
* 버그 수정

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* 일반 공급 릴리스

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-preview
* 초기 미리 보기 릴리스

## <a name="release-and-retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

[WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) 미리 보기 패키지는 사용되지 않으며 [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) 패키지로 대체되었습니다. WindowsAzure.Storage-PremiumTable SDK는 2018년 11월 15일부터 사용이 중지되고 사용 중지된 SDK에 대한 요청도 허용되지 않습니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다. 

사용 중지된 SDK를 사용하는 Azure Cosmos DB에 대한 요청은 서비스에서 거부됩니다.
<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [1.1.1](#1.1.1) |2018년 3월 26일|--- |
| [1.1.0](#1.1.0) |2018년 2월 21일|--- |
| [1.0.0](#1.0.0) |2017년 11월 15일|--- |
| [0.9.0-preview](#0.9.0-preview) |2017년 11월 11일 |--- |

## <a name="troubleshooting"></a>문제 해결

Microsoft.Azure.CosmosDB.Table NuGet 패키지를 사용하려고 할 때, 오류가 발생하면 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

문제를 해결할 수 있는 두 가지 옵션이 있습니다.

* 패키지 관리 콘솔을 사용하여 Microsoft.Azure.CosmosDB.Table 패키지와 해당 종속성을 설치합니다. 이렇게 하려면 솔루션의 패키지 관리자 콘솔에 다음을 입력합니다. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* 원하는 Nuget 패키지 관리 도구를 사용하여 Microsoft.Azure.CosmosDB.Table을 설치하기 전에 Microsoft.Azure.Storage.Common Nuget 패키지를 설치합니다.

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Azure Cosmos DB Table API에 대한 자세한 내용은 [Azure Cosmos DB Table API 소개](table-introduction.md)를 참조하세요. 
