---
title: Node.js를 사용하여 Azure Cosmos DB에 MongoDB 앱 연결 | Microsoft Docs
description: Azure Cosmos DB에 기존 Node.js MongoDB 앱을 연결하는 자세한 방법
services: cosmos-db
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/19/2017
ms.author: sngun
ms.openlocfilehash: 69302a0291b267bd4d46ab9387d296673da17025
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-migrate-an-existing-nodejs-mongodb-web-app"></a>Azure Cosmos DB: 기존 Node.js MongoDB 웹앱 마이그레이션 

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 Node.js로 작성된 기존 MongoDB 앱을 사용하고, [MongoDB API](mongodb-introduction.md)를 통해 MongoDB 클라이언트 연결을 지원하는 Azure Cosmos DB 데이터베이스에 연결하는 방법을 보여 줍니다. 즉, Node.js 응용 프로그램은 MongoDB API를 사용하여 데이터베이스에 연결됩니다. Azure Cosmos DB에 데이터가 저장되는 응용 프로그램에 대해 투명합니다.

완료하고 나면 MEAN 응용 프로그램(MongoDB, Express, Angular 및 Node.js)이 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)에서 실행됩니다. 

![Azure App Service에서 실행 중인 MEAN.js 응용 프로그램](./media/create-mongodb-nodejs/meanjs-in-azure.png)


[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="prerequisites"></a>필수 조건 
Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Azure CLI 외에도 `npm` 및 `git` 명령을 실행하려면 [Node.js](https://nodejs.org/) 및 [Git](http://www.git-scm.com/downloads)가 로컬로 설치되어 있어야 합니다.

Node.js에 대한 실무 지식이 있어야 합니다. 이 빠른 시작은 일반적으로 Node.js 응용 프로그램을 개발하는 데 도움이 되지 않습니다.

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 샘플 리포지토리에는 기본 [MEAN.js](http://meanjs.org/) 응용 프로그램이 들어 있습니다.

1. 명령 프롬프트를 git-samples라는 새 폴더를 만든 다음 명령 프롬프트를 닫습니다.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>응용 프로그램 실행

필요한 패키지를 설치하고 응용 프로그램을 시작합니다.

```bash
cd mean
npm install
npm start
```
응용 프로그램은 MongoDB 원본에 연결을 시도했다가 실패하고, 계속 진행하다가, 출력이 “[MongoError: connect ECONNREFUSED 127.0.0.1:27017]”을 반환하면 응용 프로그램을 종료합니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

설치된 Azure CLI를 사용하는 경우 [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다. Azure Cloud Shell을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Azure Cosmos DB 모듈 추가

설치된 Azure CLI를 사용하는 경우 `az` 명령을 실행하여 `cosmosdb` 구성 요소가 이미 설치되어 있는지 확인합니다. 기본 명령 목록에 `cosmosdb`가 있으면 다음 명령으로 진행합니다. Azure Cloud Shell을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

`cosmosdb`가 기본 명령 목록에 없으면 [Azure CLI 2.0]( /cli/azure/install-azure-cli)을 다시 설치합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create)를 사용하여 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. Azure 리소스 그룹은 웹앱, 데이터베이스, 저장소 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 유럽 서부 지역의 리소스 그룹을 만듭니다. 리소스 그룹에 고유한 이름을 선택합니다.

Azure Cloud Shell을 사용하는 경우 **시도**를 클릭하고, 화면의 지시에 따라 로그인한 다음, 명령 프롬프트에 명령을 복사합니다.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

[az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) 명령을 사용하여 Azure Cosmos DB 계정을 만듭니다.

다음 명령에서 `<cosmosdb-name>` 자리 표시자를 표시하는 고유한 Azure Cosmos DB 이름을 바꿉니다. 이 고유한 이름은 Azure Cosmos DB 끝점의 일부(`https://<cosmosdb-name>.documents.azure.com/`)로 사용되므로, Azure의 모든 Azure Cosmos DB 계정에서 고유해야 합니다. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

`--kind MongoDB` 매개 변수는 MongoDB 클라이언트 연결을 사용하도록 설정합니다.

Azure Cosmos DB 계정을 만든 경우 Azure CLI는 다음 예와 비슷한 정보를 표시합니다. 

> [!NOTE]
> 이 예서는 Azure CLI 출력 형식으로 JSON을 사용합니다(기본값). 다른 출력 형식을 사용하려면 [Azure CLI 2.0 명령에 대한 출력 형식](https://docs.microsoft.com/cli/azure/format-output-azure-cli)을 참조하세요.

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>데이터베이스에 Node.js 응용 프로그램 연결

이 단계에서는 MongoDB 연결 문자열을 사용하여 MEAN.js 샘플 응용 프로그램을 방금 만든 Azure Cosmos DB 데이터베이스에 연결합니다. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Node.js 응용 프로그램에 연결 문자열 구성

MEAN.js 리포지토리에서 `config/env/local-development.js`를 엽니다.

이 파일 내용을 다음 코드로 바꿉니다. 또한 두 개의 `<cosmosdb-name>` 자리 표시자를 Azure Cosmos DB 계정 이름으로 바꿔야 합니다.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>키 검색

Azure Cosmos DB 데이터베이스에 연결하기 위해 데이터베이스 키가 필요합니다. [az cosmosdb list-keys](/cli/azure/cosmosdb#list-keys) 명령을 사용하여 기본 키를 검색합니다.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI는 다음 예와 유사한 정보를 출력합니다. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

`primaryMasterKey`의 값을 복사합니다. `local-development.js`에 있는 `<primary_master_key>`에 덮어씁니다.

변경 내용을 저장합니다.

### <a name="run-the-application-again"></a>응용 프로그램을 다시 실행합니다.

`npm start`을 다시 실행합니다. 

```bash
npm start
```

이제 콘솔 메시지에서는 개발 환경이 실행된다고 알려 주어야 합니다. 

브라우저에서 `http://localhost:3000`으로 이동합니다. 맨 위 메뉴에서 **등록**을 클릭하여 두 개의 더미 사용자를 만듭니다. 

MEAN.js 샘플 응용 프로그램은 데이터베이스에 사용자 데이터를 저장합니다. 성공해서 MEAN.js가 생성된 사용자로 자동 로그인하면 Azure Cosmos DB 연결이 작동합니다. 

![MEAN.js가 MongoDB 연결에 성공](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>데이터 탐색기에서 데이터 보기

Azure Cosmos DB에서 저장된 데이터는 Azure Portal에서 비즈니스 논리 보기, 쿼리 및 실행에 사용할 수 있습니다.

이전 단계에서 만든 사용자 데이터를 보고 쿼리하고 사용하려면 웹 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.

맨 위에 있는 Search 상자에서 Azure Cosmos DB를 입력합니다. Cosmos DB 계정 블레이드가 열리면 Cosmos DB 계정을 선택합니다. 왼쪽 탐색에서 데이터 탐색기를 클릭합니다. 컬렉션 창에서 컬렉션을 확장하면 컬렉션에서 문서를 보고, 데이터를 쿼리하고 저장된 프로시저, 트리거 및 UDF를 만들고 실행합니다. 

![Azure Portal의 데이터 탐색기](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Azure에 Node.js 응용 프로그램 배포

이 단계에서는 MongoDB에 연결된 Node.js 응용 프로그램을 Azure Cosmos DB에 배포합니다.

개발 환경을 위해 이전에 변경한 구성 파일을 알 수 있습니다(`/config/env/local-development.js`). 응용 프로그램을 App Service에 배포하면 기본적으로 프로덕션 환경에서 실행합니다. 이제 해당 구성 파일을 동일하게 변경해야 합니다.

MEAN.js 리포지토리에서 `config/env/production.js`를 엽니다.

`db` 개체에서 다음 예에 표시된 것과 같이 `uri` 값을 바꿉니다. 전과 같이 자리 표시자를 바꿔야 합니다.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> `ssl=true` 옵션은 [Azure Cosmos DB에서 SSL이 필요](connect-mongodb-account.md#connection-string-requirements)하기 때문에 중요합니다. 
>
>

터미널에서 Git에 모든 변경 내용을 커밋합니다. 두 명령을 복사하여 함께 실행할 수 있습니다.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서, Azure Cosmos DB 계정을 만들고, 데이터 탐색기를 사용하여 MongoDB 컬렉션을 만드는 방법을 알아보았습니다. 이제 Azure Cosmos DB에 MongoDB 데이터를 마이그레이션할 수 있습니다.  

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 MongoDB 데이터 가져오기](mongodb-migrate.md)
