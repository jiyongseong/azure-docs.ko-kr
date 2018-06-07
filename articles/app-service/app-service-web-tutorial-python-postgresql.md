---
title: Azure에서 Python 및 PostgreSQL 웹앱 빌드 | Microsoft Docs
description: PostgreSQL 데이터베이스에 연결하여 Azure에서 Python 앱이 작동하도록 하는 방법에 대해 알아봅니다.
services: app-service\web
documentationcenter: python
author: berndverst
manager: erikre
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/25/2018
ms.author: beverst
ms.custom: mvc
ms.openlocfilehash: 49ec67d06446d6c48e45aef90e2bd528a1b541a9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="tutorial-build-a-python-and-postgresql-web-app-in-azure"></a>자습서: Azure에서 Python 및 PostgreSQL 웹앱 빌드

> [!NOTE]
> 이 문서에서는 Windows의 App Service에 앱을 배포합니다. _Linux_의 App Service에 배포하려면 [Azure에서 Docker Python 및 PostgreSQL 웹앱 빌드](./containers/tutorial-docker-python-postgresql-app.md)를 참조하세요.
>

[Azure App Service](app-service-web-overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 Azure에서 기본 Python 웹앱을 만드는 방법을 보여 줍니다. 이 앱을 PostgreSQL 데이터베이스에 연결합니다. 완료되면 App Service에서 Python Flask 응용 프로그램을 실행하게 됩니다.

![Linux의 App Service의 Python Flask 앱](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 PostgreSQL 데이터베이스 만들기
> * MySQL에 Python 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure Portal에서 앱 관리

macOS에서 이 자습서의 단계를 수행할 수 있습니다. Linux와 Windows의 지침은 대부분 동일하지만, 차이점은 이 자습서에서 자세히 설명하지 않습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

1. [Git 설치](https://git-scm.com/)
1. [Python 설치](https://www.python.org/downloads/)
1. [PostgreSQL 설치 및 실행](https://www.postgresql.org/download/)

## <a name="test-local-postgresql-installation-and-create-a-database"></a>로컬 PostgreSQL 설치 테스트 및 데이터베이스 만들기

터미널 창을 열고 `psql`를 실행하여 로컬 PostgreSQL 서버에 연결합니다.

```bash
sudo -u postgres psql
```

연결이 성공하면 PostgreSQL 데이터베이스가 실행되고 있습니다. 연결이 실패하면 [Downloads - PostgreSQL Core Distribution](https://www.postgresql.org/download/)의 단계를 수행하여 로컬 PostgresQL 데이터베이스가 시작되도록 합니다.

*eventregistration*라는 데이터베이스를 만들고, 암호가 *supersecretpass*인 *manager*라는 별도의 데이터베이스 사용자를 설정합니다.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```
`\q`를 입력하여 PostgreSQL 클라이언트를 종료합니다. 

<a name="step2"></a>

## <a name="create-local-python-flask-application"></a>로컬 Python Flask 응용 프로그램 만들기

이 단계에서는 로컬 ASP.NET 프로젝트를 설정합니다.

### <a name="clone-the-sample-application"></a>샘플 응용 프로그램 복제

터미널 창을 열고 `CD`를 사용하여 작업 디렉터리로 이동합니다.

다음 명령을 실행하여 샘플 리포지토리를 복제한 후 최초 앱(`modelChange` 이전)에 대한 커밋으로 되돌립니다.

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app
cd flask-postgresql-app
git revert modelChange --no-edit
```

이 샘플 리포지토리에는 [Flask](http://flask.pocoo.org/) 응용 프로그램이 들어 있습니다. 

### <a name="run-the-application"></a>응용 프로그램 실행

필요한 패키지를 설치하고 응용 프로그램을 시작합니다.

```bash
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

앱이 완전히 로드되면 다음과 비슷한 메시지가 표시됩니다.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

브라우저에서 `http://localhost:5000`으로 이동합니다. **Register!** 를 클릭하고 테스트 사용자를 만듭니다.

![로컬로 Python Flask 응용 프로그램 실행](./media/app-service-web-tutorial-python-postgresql/local-app.png)

Flask 샘플 응용 프로그램은 데이터베이스에 사용자 데이터를 저장합니다. 사용자 등록에 성공하면 앱에서 로컬 PostgreSQL 데이터베이스에 데이터를 쓰고 있습니다.

언제든지 Flask 서버를 중지하려면 터미널에서 Ctrl+C를 입력합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-postgresql-in-azure"></a>Azure에서 PostgreSQL 만들기

이 단계에서는 Azure에 PostgreSQL 데이터베이스를 만듭니다. Azure에 앱을 배포하면 이 클라우드 데이터베이스가 사용됩니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-postgresql-server"></a>PostgreSQL 서버 만들기

[`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) 명령을 사용하여 PostgreSQL 서버를 만듭니다.

다음 명령에서 *\<postgresql_name>* 자리 표시자를 고유한 서버 이름으로, *\<admin_username>* 을 사용자 이름으로, *\<admin_password* 자리 표시자를 암호로 대체하세요. 서버 이름은 PostgreSQL 끝점(`https://<postgresql_name>.postgres.database.azure.com`)의 일부로 사용되므로 이름은 Azure의 모든 서버에서 고유해야 합니다.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

PostgreSQL용 Azure 데이터베이스 서버를 만들면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "location": "westeurope",
  "name": "<postgresql_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "additionalProperties": {},
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  ...   +  
  -  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>서버 방화벽 구성

다음 Azure CLI 명령을 실행하여 모든 IP 주소에서 데이터베이스에 액세스할 수 있게 합니다. 시작 IP 및 끝 IP가 0.0.0.0으로 설정되면 방화벽이 다른 Azure 리소스에 대해서만 열립니다. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0 --name AllowAzureIPs
```

Azure CLI는 다음 예제와 비슷한 출력으로 방화벽 규칙 만들기를 확인합니다.

```json
{
  "additionalProperties": {},
  "endIpAddress": "255.255.255.255",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAllIPs",
  "name": "AllowAllIPs",
 "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

> [!TIP] 
> [앱이 사용하는 아웃바운드 IP 주소만 사용](app-service-ip-addresses.md#find-outbound-ips)으로 방화벽 규칙을 훨씬 더 엄격하게 제한할 수 있습니다.
>

### <a name="create-a-production-database-and-user"></a>프로덕션 데이터베이스 및 사용자 만들기

단일 데이터베이스에만 액세스할 수 있는 새 데이터베이스 사용자를 만듭니다. 서버에 대한 모든 액세스 권한을 응용 프로그램에 부여하지 않도록 하기 위해 이러한 자격 증명을 사용합니다.

데이터베이스에 연결합니다(관리자 암호를 묻는 메시지가 표시됨).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <admin_username>@<postgresql_name> postgres
```

PostgreSQL CLI에서 데이터베이스와 사용자를 만듭니다.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

`\q`를 입력하여 PostgreSQL 클라이언트를 종료합니다.

### <a name="test-app-locally-with-azure-postgresql"></a>Azure PostgreSQL을 사용하여 로컬에서 앱 테스트

이제 복제된 Github 리포지토리의 *app* 폴더로 돌아가서 데이터베이스 환경 변수를 업데이트하여 Python Flask 응용 프로그램을 실행할 수 있습니다.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

앱이 완전히 로드되면 다음과 비슷한 메시지가 표시됩니다.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

브라우저에서 http://localhost:5000으로 이동합니다. **Register!** 를 클릭하고 테스트 등록을 만듭니다. 이제 Azure에서 데이터베이스에 데이터를 쓰고 있습니다.

![로컬로 Python Flask 응용 프로그램 실행](./media/app-service-web-tutorial-python-postgresql/local-app.png)

## <a name="deploy-to-azure"></a>Deploy to Azure

이 단계에서는 PostgreSQL에 연결된 Python 응용 프로그램을 Azure App Service에 배포합니다.

Git 리포지토리에는 App Service에서 Flask 웹앱을 실행하는 데 필요한 다음과 같은 파일이 이미 포함되어 있습니다.

- `.deployment`: 실행할 사용자 지정 배포 스크립트를 지정합니다.
- `deploy.cmd`: 배포 스크립트입니다. 여기서 `pip install`이 실행됩니다.
- `web.config`: IIS의 `httpPlatformHandler`에서 실행할 진입점 스크립트를 지정합니다.
- `run_waitress_server.py`: 진입점 스크립트입니다. [`waitress`](https://docs.pylonsproject.org/projects/waitress) 서버에서 Flask 웹앱을 시작합니다.

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service 계획 만들기

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>웹앱 만들기

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-no-h.md)] 

### <a name="install-python"></a>Python 설치

이 단계에서는 App Service에서 [사이트 확장](https://www.siteextensions.net/packages?q=Tags%3A%22python%22)을 통해 Python 3.6.2를 설치합니다. [배포 사용자 구성](#configure-a-deployment-user)에서 구성한 자격 증명을 사용하여 REST 엔드포인트에 대해 인증합니다.

Python 3.6.2 패키지 정보를 가져오려면 Cloud Shell에서 다음 명령을 실행합니다. *\<deployment_user>* 는 이전에 구성한 배포 사용자 이름으로 바꾸고, *\<app_name>* 은 앱 이름으로 바꿉니다. 메시지가 표시되면 이전에 구성한 배포 암호를 사용합니다.

```bash
packageinfo=$(curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/extensionfeed/python362x86)
```

Python 패키지를 설치하려면 Cloud Shell에서 다음 명령을 실행합니다. *\<deployment_user>* 는 이전에 구성한 배포 사용자 이름으로 바꾸고, *\<app_name>* 은 앱 이름으로 바꿉니다. 메시지가 표시되면 이전에 구성한 배포 암호를 사용합니다.

```bash
curl -X PUT -u <deployment_user> -H "Content-Type: application/json" -d '$packageinfo' https://<app_name>.scm.azurewebsites.net/api/siteextensions/python362x86
```

명령 출력에서 `D:\home\python362x86\python.exe` 경로에 Python이 설치된 것을 볼 수 있습니다.

### <a name="configure-database-settings"></a>데이터베이스 설정 구성

자습서의 앞부분에서 환경 변수를 정의하여 PostgreSQL 데이터베이스에 연결했습니다.

App Service에서 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 명령을 사용하여 환경 변수를 _앱 설정_으로 설정합니다.

다음 예제에서는 데이터베이스 연결 세부 정보를 앱 설정으로 지정합니다. *\<app_name>* 을 앱 이름으로 바꾸고 *\<postgresql_name>* 을 PostgreSQL 서버 이름으로 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기 

웹 브라우저를 사용하여 배포된 웹앱으로 이동합니다. 

```bash 
http://<app_name>.azurewebsites.net 
```

이전 단계에서 Azure 프로덕션 데이터베이스에 저장된 이전에 등록한 게스트를 볼 수 있습니다.

![로컬로 Python Flask 응용 프로그램 실행](./media/app-service-web-tutorial-python-postgresql/docker-app-deployed.png)

**축하합니다.** Azure App Service에서 Python Flask 앱이 실행되고 있습니다.

## <a name="update-data-model-and-redeploy"></a>데이터 모델 업데이트 및 다시 배포

이 단계에서는 `Guest` 모델을 업데이트하여 각 이벤트 등록에 대한 참석자 수를 추가합니다.

`modelChange` 커밋으로 태그가 지정된 파일을 확인하세요.

```bash
git checkout modelChange -- *
```

이 릴리스에는 보기, 컨트롤러 및 모델에 필요한 변경 내용이 이미 적용되었습니다. 또한 *alembic*(`flask db migrate`)를 통해 생성된 데이터베이스 마이그레이션도 포함됩니다. [GitHub 커밋 보기](https://github.com/Azure-Samples/flask-postgresql-app/commit/139a53023688631c3cc2caefd70086f4722ecd7e)에서 모든 파일에 대한 변경 내용을 볼 수 있습니다.

### <a name="test-your-changes-locally"></a>변경 내용을 로컬에서 테스트

다음 명령을 통해 Flask 서버를 로컬로 실행하여 로컬에서 변경 내용을 테스트합니다.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

브라우저에서 http://localhost:5000으로 이동하여 변경 내용을 확인합니다. 테스트 등록을 만듭니다.

![로컬로 Python Flask 응용 프로그램 실행](./media/app-service-web-tutorial-python-postgresql/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

로컬 터미널 창에서 Git의 모든 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git add .
git commit -m "updated data model"
git push azure master
```

Azure Web App으로 이동하여 새 기능을 다시 시험해 봅니다. 다른 이벤트 등록을 만듭니다.

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure App Service의 Python Flask 앱](./media/app-service-web-tutorial-python-postgresql/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Azure Web App 관리

[Azure Portal](https://portal.azure.com)로 이동하여 만든 웹앱을 확인합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/app-service-web-tutorial-python-postgresql/app-resource.png)

기본적으로 포털에는 웹앱의 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 페이지](./media/app-service-web-tutorial-python-postgresql/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

다음 자습서로 이동하여 사용자 지정 DNS 이름을 웹앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)
