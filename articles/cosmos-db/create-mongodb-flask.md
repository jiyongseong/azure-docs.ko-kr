---
title: 'Azure Cosmos DB: Python 및 Azure Cosmos DB MongoDB API로 Flask 웹앱 빌드 | Microsoft Docs'
description: Azure Cosmos DB MongoDB API에 연결하고 및 쿼리하는 데 사용할 수 있는 Python Flask 코드 샘플을 제시합니다.
services: cosmos-db
documentationcenter: ''
author: heatherbshapiro
manager: kfile
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/2/2017
ms.author: hshapiro
ms.openlocfilehash: 095cc724beb9f35896bd02e299523839a9f43f4b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885105"
---
# <a name="azure-cosmos-db-build-a-flask-app-with-the-mongodb-api"></a>Azure Cosmos DB: MongoDB API로 Flask 앱 빌드

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다.

이 빠른 시작 가이드에서는 다음 [Flask 예제](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample)를 사용하고, MongoDB 대신 [Azure Cosmos DB Emulator](local-emulator.md) 및 Azure Cosmos DB [MongoDB API](mongodb-introduction.md)를 통해 간단한 To-Do(할 일) Flask 앱을 빌드하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

- [Azure Cosmos DB Emulator](local-emulator.md)를 다운로드합니다. 에뮬레이터는 현재 Windows에서만 지원됩니다. 샘플은 모든 플랫폼에서 수행할 수 있는 Azure의 제품 키로 샘플을 사용하는 방법을 보여 줍니다.

- Visual Studio Code를 아직 설치하지 않은 경우 자신의 플랫폼(Windows, Mac, Linux)에 맞는 [VS Code](https://code.visualstudio.com/Download)를 빠르게 설치할 수 있습니다.

- 인기 있는 Python 확장 중 하나를 설치하여 Python 언어 지원을 추가해야 합니다.
    1. 확장을 선택합니다.
    2. `Ctrl+Shift+P` 명령 팔레트에 `ext install`을 입력하여 확장을 설치합니다.

    이 문서의 예제에서는 널리 사용되고 완벽한 Don Jayamanne의 [Python 확장](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python)을 사용합니다.

## <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

이제 GitHub에서 Flask MongoDB API 앱을 복제하고 연결 문자열을 설정한 다음 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다.

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
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. 다음 명령을 실행하여 Python 모듈을 설치합니다.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Visual Studio Code에서 해당 폴더를 엽니다.

## <a name="review-the-code"></a>코드 검토

이 단계는 옵션입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [웹앱 실행](#run-the-web-app)으로 넘어갈 수 있습니다. 

다음 코드 조각은 모두 app.py 파일에서 가져오고 로컬 Azure Cosmos DB 에뮬레이터에 대한 연결 문자열을 사용합니다. 그렇지 않으면 구문 분석할 수 없는 슬래시를 받아들이도록 아래와 같이 암호를 분할해야 합니다.

* MongoDB 클라이언트를 초기화하고, 데이터베이스를 검색하고, 인증합니다.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* 컬렉션을 검색하거나 아직 없는 경우 컬렉션을 만듭니다.

    ```python
    todos = db.todo #Select the collection
    ```

* 앱 만들기

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>웹앱 실행

1. Azure Cosmos DB Emulator가 실행 중인지 확인합니다.

2. 터미널 창을 열고 `cd`로 앱이 저장된 디렉터리로 이동합니다.

3. 그런 다음 Mac을 사용하는 경우 Flask 앱에 대한 환경 변수를 `set FLASK_APP=app.py` 또는 `export FLASK_APP=app.py`로 설정합니다.

4. `flask run`을 사용하여 앱을 실행하고 [http://127.0.0.1:5000/](http://127.0.0.1:5000/)으로 이동합니다.

5. 작업을 추가하거나 제거하고, 컬렉션에서 추가하거나 변경된 작업을 확인합니다.

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

라이브 Azure Cosmos DB 계정에 대해 코드를 테스트하려면 Azure Portal로 이동하여 계정을 만들고 연결 문자열 정보를 가져옵니다. 그런 다음 앱에 복사합니다.

1. [Azure Portal](http://portal.azure.com/)의 Azure Cosmos DB 계정에서 왼쪽 탐색 영역의 **연결 문자열**을 클릭한 다음 **읽기-쓰기 키**를 클릭합니다. 다음 단계에서 화면의 오른쪽에 있는 복사 단추를 사용하여 사용자, 암호 및 호스트를 Dal.cs 파일에 복사합니다.

2. 루트 디렉터리에 있는 **app.py** 파일을 엽니다.

3. 포털에서 복사 단추를 사용하여 **사용자 이름** 값을 복사하고 **app.py** 파일의 **이름** 값으로 만듭니다.

4. 그런 다음 포털에서 **연결 문자열** 값을 복사하고 **app.py** 파일의 MongoClient 값으로 만듭니다.

5. 마지막으로, 포털에서 사용자의 **암호** 값을 복사하고 **app.py** 파일의 **암호** 값으로 만듭니다.

이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 이전과 같은 방식으로 실행할 수 있습니다.

## <a name="deploy-to-azure"></a>Deploy to Azure

이 앱을 배포하려면 Azure에서 새 웹앱을 만들고 이 GitHub 리포지토리의 포크로 지속적인 배포를 사용하도록 설정할 수 있습니다. 이 [자습서](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment)에 따라 Azure에서 GitHub를 사용하여 지속적인 배포를 설정합니다.

Azure에 배포할 때 응용 프로그램 키를 제거하고 아래 섹션이 주석 처리되어 있지 않은지 확인해야 합니다.

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

그런 다음 MONGOURL, MONGO_PASSWORD 및 MONGO_USERNAME을 응용 프로그램 설정에 추가해야 합니다. Azure Web Apps의 응용 프로그램 설정에 대한 자세한 내용은 이 [자습서](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings)를 참조하세요.

이 리포지토리의 포크를 만들지 않으려면 아래의 [Azure에 배포] 단추를 클릭할 수도 있습니다. 그런 다음 Azure로 이동하고 Cosmos DB 계정 정보를 사용하여 응용 프로그램 설정을 지정해야 합니다.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="http://azuredeploy.net/deploybutton.png"/>
</a>

> [!NOTE]
> GitHub 또는 다른 원본 제어 옵션에 코드를 저장하려면 코드에서 연결 문자열을 제거합니다. 대신 웹앱에 대한 응용 프로그램 설정을 사용하여 지정할 수 있습니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cosmos DB 계정을 만들고, MongoDB용 API를 사용하여 Flask 앱을 실행하는 방법을 알아보았습니다. 이제 Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다.

> [!div class="nextstepaction"]
> [MongoDB API용 Azure Cosmos DB로 데이터 가져오기](mongodb-migrate.md)
