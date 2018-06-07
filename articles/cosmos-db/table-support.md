---
title: Azure Cosmos DB에서 Azure Table Storage 지원 | Microsoft Docs
description: Azure Cosmos DB Table API 및 Azure Storage Tables이 함께 작동하는 방법에 대해 알아봅니다.
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 2560c2ee34a83ce86db043e17fb41192c31de398
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Azure Cosmos DB Table API 및 Azure Table storage로 개발하기

Azure Cosmos DB 테이블 API와 Azure Table storage는 동일한 테이블 데이터 모델을 공유하고, 해당 SDK를 통해 동일한 만들기, 삭제, 업데이트 및 쿼리 작업을 노출합니다. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API를 사용하여 개발

현재 [Azure Cosmos DB Table API](table-introduction.md)에는 개발에 사용할 수 있는 네 개의 SDK가 있습니다. 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK. 이 라이브러리는 공용 [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage)와 동일한 클래스 및 메서드 서명을 갖추고 있지만 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있습니다. 
- [Python SDK](table-sdk-python.md). 새 Azure Cosmos DB Python SDK는 Python에서 Azure Table Storage를 지원하는 유일한 SDK입니다. 이 SDK는 Azure Table Storage 및 Azure Cosmos DB Table API 모두와 연결됩니다.
- [Java SDK](table-sdk-java.md). 이 Azure Storage SDK에는 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있는 기능이 있습니다.
- [Node.js SDK](table-sdk-nodejs.md). 이 Azure Storage SDK에는 Table API를 사용하여 Azure Cosmos DB 계정에 연결할 수 있는 기능이 있습니다.

Table API를 사용하여 작업하는 방법에 관한 추가 정보는 [FAQ: Table API를 사용하여 개발](faq.md#develop-with-the-table-api) 문서에서 제공됩니다.

## <a name="developing-with-the-azure-table-storage"></a>Azure Table Storage를 사용하여 개발

[Azure Table Storage](table-storage-overview.md)에는 제공되는 SDK 및 자습서가 많으며, 현재 그 모든 것은 [Azure Table Storage](table-storage-overview.md) 섹션에서 제공됩니다. Azure Table Storage SDK 및 Azure Cosmos DB Table API 간의 상호 운용성이 제공되도록 이러한 문서가 업데이트되고 있습니다.  





