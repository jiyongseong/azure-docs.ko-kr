---
title: Azure에 대한 MongoDB, Angular 및 노드 자습서 - 4부 | Microsoft Docs
description: MongoDB에 사용하는 것과 정확히 동일한 API를 사용한 Azure Cosmos DB의 Angular 및 노드를 사용하여 MongoDB 앱을 만드는 방법에 대한 자습서 시리즈의 4부
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: c9f0c6f18645a8d3a5a069aa55fec0ba8e9ba547
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-4-create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Angular 및 Azure Cosmos DB를 사용하여 MongoDB 앱 만들기 - 4부: Azure CLI를 사용하여 Azure Cosmos DB 계정 만들기

이 다중 파트 자습서에서는 Express, Angular Azure Cosmos DB 데이터베이스를 포함한 Node.js로 작성된 새 [MongoDB API](mongodb-introduction.md) 앱을 만드는 방법을 보여줍니다.

자습서의 4부는 [3부](tutorial-develop-mongodb-nodejs-part3.md)를 기반으로 하고 다음과 같은 작업을 다룹니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure 리소스 그룹 만들기
> * Azure CLI를 사용하여 Azure Cosmos DB 계정 만들기

## <a name="video-walkthrough"></a>연습 동영상

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>필수 조건

자습서의 이 부분을 시작하기 전에 자습서 [3부](tutorial-develop-mongodb-nodejs-part3.md)의 단계를 완료했는지 확인합니다. 

이 자습서 섹션에서는 (인터넷 브라우저에서) 로컬로 설치된 Azure Cloud Shell 또는 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)을 사용합니다. 로컬로 Azure CLI를 사용하면 Azure CLI 버전 2.0 이상을 실행하는지 확인합니다. 명령 프롬프트에서 `az --version`을 실행하여 버전을 확인합니다. 

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> 이 자습서에서는 응용 프로그램을 구축하는 단계를 단계별로 설명합니다. 완료된 프로젝트를 다운로드하려는 경우 GitHub의 [angular-cosmosdb repo](https://github.com/Azure-Samples/angular-cosmosdb)에서 완성된 응용 프로그램을 가져올 수 있습니다.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

[`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create) 명령을 사용하여 Azure Cosmos DB 계정을 만듭니다.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* `<cosmosdb-name>`이 고유한 Azure Cosmos DB 계정 이름을 사용하는 경우 해당 이름은 Azure에 있는 모든 Azure Cosmos DB 계정 이름에 대해 고유해야 합니다.
* `--kind MongoDB` 설정을 사용하면 Azure Cosmos DB에서 MongoDB 클라이언트 연결을 사용하도록 설정합니다.

명령이 완료되는 데 일 분 정도 걸릴 수 있습니다. 작업이 완료되면 터미널 창에는 새 데이터베이스에 대한 정보가 표시됩니다. 

Cosmos DB Azure 계정을 만들면:
1. 새 브라우저 창을 열고 [https://portal.azure.com](https://portal.azure.com)으로 이동합니다.
1. Azure Cosmos DB 로고를 클릭합니다. ![Azure Portal의 Azure Cosmos DB 아이콘](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) 왼쪽 모음에서 모든 Azure Cosmos DB를 표시합니다.
1. 방금 만든 Azure Cosmos DB 계정을 클릭하고, **개요** 탭을 선택하고, 아래로 스크롤하여 데이터베이스가 위치한 맵을 봅니다. 

    ![Azure Portal의 새 Azure Cosmos DB 계정](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. 왼쪽 탐색 창에서 아래로 스크롤하고 **데이터를 전역적으로 복제** 탭을 클릭하면 복제 가능한 다른 영역을 볼 수 있는 맵을 표시합니다. 예를 들어, 오스트레일리아 동남부 또는 오스트레일리아 동부를 클릭하고 오스트레일리아에 데이터를 복제할 수 있습니다. [Azure Cosmos DB를 사용하여 전역적으로 데이터를 배포하는 방법](distribute-data-globally.md)에서 전역 복제에 대해 자세히 알아볼 수 있습니다. 지금은 이전 인스턴스 및 복제할 시기를 유지하겠습니다.

    ![Azure Portal의 새 Azure Cosmos DB 계정](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 Azure 리소스 그룹 만들기
> * Azure CLI를 사용하여 Azure Cosmos DB 계정 만들기

Mongoose를 사용하여 Azure Cosmos DB를 앱에 연결하는 자습서의 다음 단계로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Mongoose를 사용하여 Azure Cosmos DB에 연결](tutorial-develop-mongodb-nodejs-part5.md)
