---
title: Graph API의 Azure Cosmos DB 전역 배포 자습서 | Microsoft Docs
description: Graph API를 사용하여 Azure Cosmos DB 전역 배포를 설정하는 방법에 대해 알아봅니다.
services: cosmos-db
keywords: 전역 배포, 그래프, Gremlin
documentationcenter: ''
author: luisbosquez
manager: kfile
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 273b5aeafbf67016259da787f4dfef078ec0a669
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Graph API를 사용하여 Azure Cosmos DB 전역 배포를 설정하는 방법

이 문서에서는 Azure Portal을 사용하여 Azure Cosmos DB 전역 배포를 설정한 다음, Graph API를 사용하여 연결하는 방법을 보여 줍니다.

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * [Graph API](graph-introduction.md)를 사용하여 전역 배포 구성

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>.NET SDK를 사용하는 Graph API를 사용하여 기본 설정 지역에 연결

Graph API는 SQL API를 기반으로 하는 확장 라이브러리로 제공됩니다.

[전역 배포](distribute-data-globally.md)를 활용하기 위해 클라이언트 응용 프로그램은 문서 작업을 수행하는 데 사용할 정렬된 기본 지역 목록을 지정할 수 있습니다. 이는 연결 정책을 설정하여 수행할 수 있습니다. SDK에서 Azure Cosmos DB 계정 구성, 현재 지역 가용성 및 지정된 기본 설정 목록을 기반으로 하여 쓰기 및 읽기 작업을 수행하는 데 가장 적합한 끝점을 선택합니다.

이 기본 설정 목록은 SDK를 사용하여 연결을 초기화할 때 지정됩니다. SDK는 Azure 지역의 정렬된 목록인 "PreferredLocations"라는 선택적 매개 변수를 수락합니다.

* **쓰기**: SDK는 현재 쓰기 지역에 모든 쓰기를 자동으로 보냅니다.
* **읽기**: 모든 읽기는 PreferredLocations 목록에서 사용 가능한 첫 번째 지역으로 보냅니다. 요청이 실패하면 클라이언트는 목록의 다음 지역으로 옮겨갑니다. SDK는 PreferredLocations에 지정된 지역에서만 읽기를 시도합니다. 예를 들어 Cosmos DB 계정을 세 지역에서 사용할 수 있지만 클라이언트에서 PreferredLocations에 대해 쓰기에 해당하지 않는 지역 두 개만 지정하면 장애 조치(failover) 시에도 쓰기 지역에서 읽기를 제공하지 않습니다.

응용 프로그램은 두 가지 속성(WirteEndpoint 및 ReadEndpoint)을 확인하여 SDK가 선택한 현재의 쓰기 끝점과 읽기 끝점을 확인할 수 있습니다. SDK 버전 1.8 이상부터 사용 가능합니다. PreferredLocations 속성을 설정하지 않으면 모든 요청은 현재 쓰기 지역에서 제공됩니다.

### <a name="using-the-sdk"></a>SDK 사용

예를 들어 .NET SDK에서 `DocumentClient` 생성자의 `ConnectionPolicy` 매개 변수에는 `PreferredLocations`라는 속성이 있습니다. 이 속성은 지역 이름 목록으로 설정할 수 있습니다. [Azure 지역][regions]의 표시 이름은 `PreferredLocations`의 일부로 지정할 수 있습니다.

> [!NOTE]
> 끝점의 URL은 수명이 긴 상수로 간주하지 말아야 합니다. 서비스는 언제든지 이 URL을 업데이트할 수 있습니다. SDK가 이런 변경 내용을 자동으로 처리합니다.
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

이것으로 끝이며, 이 자습서를 완료했습니다! [Azure Cosmos DB의 일관성 수준](consistency-levels.md)을 참조하여 전역적으로 복제한 계정의 일관성을 관리하는 방법에 대해 알아볼 수 있습니다. 그리고 Azure Cosmos DB에서 전역 데이터베이스 복제가 작동하는 방법에 대한 자세한 내용은 [Azure Cosmos DB를 사용하여 전역적으로 데이터 배포](distribute-data-globally.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * SQL API를 사용하여 전역 배포 구성

이제 다음 자습서로 진행하여 Azure Cosmos DB 로컬 에뮬레이터를 사용하여 로컬로 개발하는 방법에 대해 자세히 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [에뮬레이터를 사용하여 로컬로 개발](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

