---
title: Azure에서 Docker Python 및 PostgreSQL 웹앱 빌드 | Microsoft Docs
description: PostgreSQL 데이터베이스에 연결하여 Azure에서 Docker Python 앱이 작동하도록 하는 방법에 대해 알아봅니다.
services: app-service\web
documentationcenter: python
author: berndverst
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 01/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: 2728c354a84c4b13b0ad8509d038837733251975
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Azure에서 Docker Python 및 PostgreSQL 웹앱 빌드

Web App for Containers는 확장성이 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 Azure에서 기본 Docker Python 웹앱을 만드는 방법을 보여 줍니다. 이 앱을 PostgreSQL 데이터베이스에 연결합니다. 완료되면 [Linux의 App Service](app-service-linux-intro.md)의 Docker 컨테이너 내에서 Python Flask 응용 프로그램을 실행하게 됩니다.

![Linux의 App Service의 Docker Python Flask 앱](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 PostgreSQL 데이터베이스 만들기
> * PostgreSQL에 Python 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure Portal에서 앱 관리

macOS에서 이 문서의 단계를 수행할 수 있습니다. Linux와 Windows의 지침은 대부분 동일하지만, 차이점은 이 자습서에서 자세히 설명하지 않습니다.
 
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

1. [Git 설치](https://git-scm.com/)
1. [Python 설치](https://www.python.org/downloads/)
1. [PostgreSQL 설치 및 실행](https://www.postgresql.org/download/)
1. [Docker Community Edition 설치](https://www.docker.com/community-edition)

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

다음 명령을 실행하여 샘플 리포지토리를 복제하고 *0.1-initialapp* 릴리스로 이동합니다.

```bash
git clone https://github.com/Azure-Samples/docker-flask-postgres.git
cd docker-flask-postgres
git checkout tags/0.1-initialapp
```

이 샘플 리포지토리에는 [Flask](http://flask.pocoo.org/) 응용 프로그램이 들어 있습니다. 

### <a name="run-the-application"></a>응용 프로그램 실행

> [!NOTE] 
> 이후 단계에서 프로덕션 데이터베이스에서 사용할 Docker 컨테이너를 빌드하여 이 프로세스를 간소화합니다.

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

![로컬로 Python Flask 응용 프로그램 실행](./media/tutorial-docker-python-postgresql-app/local-app.png)

Flask 샘플 응용 프로그램은 데이터베이스에 사용자 데이터를 저장합니다. 사용자 등록에 성공하면 앱에서 로컬 PostgreSQL 데이터베이스에 데이터를 쓰고 있습니다.

언제든지 Flask 서버를 중지하려면 터미널에서 Ctrl+C를 입력합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-production-postgresql-database"></a>프로덕션 PostgreSQL 데이터베이스 만들기

이 단계에서는 Azure에 PostgreSQL 데이터베이스를 만듭니다. Azure에 앱을 배포하면 이 클라우드 데이터베이스가 사용됩니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure Database 서버 만들기

[`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create) 명령을 사용하여 PostgreSQL 서버를 만듭니다.

다음 명령에서 *\<postgresql_name>* 자리 표시자를 대신하여 고유한 서버 이름으로, *\<admin_username>* 자리 표시자를 대신하여 사용자 이름으로 바꿉니다. 서버 이름은 PostgreSQL 끝점(`https://<postgresql_name>.postgres.database.azure.com`)의 일부로 사용되므로 이름은 Azure의 모든 서버에서 고유해야 합니다. 사용자 이름은 초기 데이터베이스 관리 사용자 계정에 대한 이름입니다. 이 사용자의 암호를 선택하라는 메시지가 표시됩니다.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --admin-user <admin_username>  --storage-size 51200
```

PostgreSQL용 Azure 데이터베이스 서버를 만들면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "administratorLogin": "<my_admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 100,
    "family": null,
    "name": "PGSQLS3M100",
    "size": null,
    "tier": "Basic"
  },
  "sslEnforcement": null,
  "storageMb": 2048,
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": null
}
```

### <a name="create-a-firewall-rule-for-the-azure-database-for-postgresql-server"></a>PostgreSQL 서버용 Azure 데이터베이스에 대한 방화벽 규칙 만들기

다음 Azure CLI 명령을 실행하여 모든 IP 주소에서 데이터베이스에 액세스할 수 있게 합니다. 시작 IP 및 끝 IP가 0.0.0.0으로 설정되면 방화벽이 다른 Azure 리소스에 대해서만 열립니다. 

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAzureIPs
```

Azure CLI는 다음 예제와 비슷한 출력으로 방화벽 규칙 만들기를 확인합니다.

```json
{
  "endIpAddress": "0.0.0.0",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>/firewallRules/AllowAzureIPs",
  "name": "AllowAzureIPs",
  "resourceGroup": "myResourceGroup",
  "startIpAddress": "0.0.0.0",
  "type": "Microsoft.DBforPostgreSQL/servers/firewallRules"
}
```

> [!TIP] 
> [앱이 사용하는 아웃바운드 IP 주소만 사용](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)으로 방화벽 규칙을 훨씬 더 엄격하게 제한할 수 있습니다.
>

## <a name="connect-your-python-flask-application-to-the-database"></a>데이터베이스에 Python Flask 응용 프로그램 연결

이 단계에서 Python Flask 샘플 응용 프로그램을 만든 PostgreSQL용 Azure 데이터베이스 서버에 연결합니다.

### <a name="create-an-empty-database-and-set-up-a-new-database-application-user"></a>빈 데이터베이스 만들기 및 새 데이터베이스 응용 프로그램 사용자 설정

단일 데이터베이스에만 액세스할 수 있는 새 데이터베이스 사용자를 만듭니다. 서버에 대한 모든 액세스 권한을 응용 프로그램에 부여하지 않도록 하기 위해 이러한 자격 증명을 사용합니다.

데이터베이스에 연결합니다(관리자 암호를 묻는 메시지가 표시됨).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

PostgreSQL CLI에서 데이터베이스와 사용자를 만듭니다.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

`\q`를 입력하여 PostgreSQL 클라이언트를 종료합니다.

### <a name="test-the-application-locally-against-the-azure-postgresql-database"></a>Azure PostgreSQL 데이터베이스에 대해 로컬로 응용 프로그램 테스트

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

![로컬로 Python Flask 응용 프로그램 실행](./media/tutorial-docker-python-postgresql-app/local-app.png)

### <a name="running-the-application-from-a-docker-container"></a>Docker 컨테이너에서 응용 프로그램 실행

Docker 컨테이너 이미지를 빌드합니다.

```bash
cd ..
docker build -t flask-postgresql-sample .
```

Docker는 컨테이너를 성공적으로 만들었다는 확인을 표시합니다.

```bash
Successfully built 7548f983a36b
```

리포지토리 루트에서 _db.env_라는 환경 변수 파일을 추가한 후 다음과 같은 데이터베이스 환경 변수를 추가합니다. 앱이 Azure Database for PostgreSQL 프로덕션 데이터베이스에 연결합니다.

```text
DBHOST=<postgresql_name>.postgres.database.azure.com
DBUSER=manager@<postgresql_name>
DBNAME=eventregistration
DBPASS=supersecretpass
```

Docker 컨테이너 내에서 앱을 실행합니다. 다음 명령은 환경 변수 파일을 지정하고 5000 기본 Flask 포트를 5000 로컬 포트에 매핑합니다.

```bash
docker run -it --env-file db.env -p 5000:5000 flask-postgresql-sample
```

출력은 이전에 표시된 것과 비슷합니다. 그러나 초기 데이터베이스 마이그레이션은 더 이상 수행할 필요가 없으므로 건너뜁니다.

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
 * Serving Flask app "app"
 * Running on http://0.0.0.0:5000/ (Press CTRL+C to quit)
```

데이터베이스에는 이미 이전에 만든 등록이 포함되어 있습니다.

![로컬로 실행되는 Docker 컨테이너 기반 Python Flask 응용 프로그램](./media/tutorial-docker-python-postgresql-app/local-docker.png)

## <a name="upload-the-docker-container-to-a-container-registry"></a>Docker 컨테이너를 컨테이너 레지스트리로 업로드

이 단계에서는 Docker 컨테이너를 컨테이너 레지스트리로 업로드합니다. Azure Container Registry를 사용하지만 Docker 허브와 같이 인기 있는 다른 레지스트리를 사용할 수도 있습니다.

### <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기

컨테이너 레지스트리를 만드는 다음 명령에서 *\<registry_name>* 을 선택한 Azure Container Registry의 고유한 이름으로 바꿉니다.

```azurecli-interactive
az acr create --name <registry_name> --resource-group myResourceGroup --location "West US" --sku Basic
```

출력

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-05-04T08:50:55.635688+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/<registry_name>",
  "location": "westus",
  "loginServer": "<registry_name>.azurecr.io",
  "name": "<registry_name>",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "<registry_name>01234"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

### <a name="retrieve-the-registry-credentials-for-pushing-and-pulling-docker-images"></a>Docker 이미지 밀어넣기 및 끌어오기를 위한 레지스트리 자격 증명 검색

레지스트리 자격 증명을 표시하려면 먼저 관리자 모드를 활성화합니다.

```azurecli-interactive
az acr update --name <registry_name> --admin-enabled true
az acr credential show -n <registry_name>
```

두 개의 암호가 표시됩니다. 사용자 이름 및 첫 번째 암호를 기록해 둡니다.

```json
{
  "passwords": [
    {
      "name": "password",
      "value": "<registry_password>"
    },
    {
      "name": "password2",
      "value": "<registry_password2>"
    }
  ],
  "username": "<registry_name>"
}
```

### <a name="upload-your-docker-container-to-azure-container-registry"></a>Docker 컨테이너를 Azure Container Registry로 업로드

레지스트리에 로그인합니다. 메시지가 표시되면 방금 전에 표시된 암호를 입력합니다.

```bash
docker login <registry_name>.azurecr.io -u <registry_name>
```

Docker 이미지를 레지스트리에 밀어넣습니다.

```bash
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
```

## <a name="deploy-the-docker-python-flask-application-to-azure"></a>Azure에 Docker Python Flask 응용 프로그램 배포

이 단계에서는 Docker 컨테이너 기반 Python Flask 응용 프로그램을 Azure App Service에 배포합니다.

### <a name="create-an-app-service-plan"></a>App Service 계획 만들기

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>웹앱 만들기

[`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) 명령을 사용하여 *myAppServicePlan* App Service 계획에 웹앱을 만듭니다.

웹앱은 코드를 배포할 호스팅 공간을 제공하고, 배포된 응용 프로그램을 확인할 수 있도록 URL도 제공합니다. 웹앱을 만드는 데 사용합니다.

다음 명령에서 *\<app_name>* 자리 표시자를 고유한 앱 이름으로 바꿉니다. 이 이름은 웹앱에 대한 URL의 일부이므로 Azure App Service의 모든 앱에서 고유해야 합니다.

```azurecli
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-container-image-name "<registry_name>.azurecr.io/flask-postgresql-sample"
```

웹앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
  ...
  < Output has been truncated for readability >
}
```

### <a name="configure-the-database-environment-variables"></a>데이터베이스 환경 변수 구성

자습서의 앞부분에서 환경 변수를 정의하여 PostgreSQL 데이터베이스에 연결했습니다.

App Service에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 명령을 사용하여 환경 변수를 _앱 설정_으로 설정합니다.

다음 예제에서는 데이터베이스 연결 세부 정보를 앱 설정으로 지정합니다. 또한 *PORT* 변수를 통해 Docker 컨테이너에서 PORT 5000을 매핑하여 PORT 80에서 HTTP 트래픽을 수신합니다.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration" PORT=5000
```

### <a name="configure-docker-container-deployment"></a>Docker 컨테이너 배포 구성

AppService는 Docker 컨테이너를 자동으로 다운로드하여 실행할 수 있습니다.

```azurecli
az webapp config container set --resource-group myResourceGroup --name <app_name> --docker-registry-server-user "<registry_name>" --docker-registry-server-password "<registry_password>" --docker-registry-server-url "https://<registry_name>.azurecr.io"
```

Docker 컨테이너를 업데이트하거나 설정을 변경할 때마다 앱을 다시 시작합니다. 다시 시작하면 모든 설정이 적용되고 레지스트리에서 최신 컨테이너를 가져옵니다.

```azurecli-interactive
az webapp restart --resource-group myResourceGroup --name <app_name>
```

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기 

웹 브라우저를 사용하여 배포된 웹앱으로 이동합니다. 

```bash 
http://<app_name>.azurewebsites.net 
```
> [!NOTE]
> 컨테이너 구성을 변경한 후에 컨테이너를 다운로드하고 시작해야 하기 때문에 웹앱에서 로드하는 데 시간이 오래 걸립니다.

이전 단계에서 Azure 프로덕션 데이터베이스에 저장된 이전에 등록한 게스트를 볼 수 있습니다.

![로컬로 실행되는 Docker 컨테이너 기반 Python Flask 응용 프로그램](./media/tutorial-docker-python-postgresql-app/docker-app-deployed.png)

**축하합니다.** Azure App Service에서 Docker 컨테이너 기반 Python Flask 앱을 실행하고 있습니다.

## <a name="update-data-model-and-redeploy"></a>데이터 모델 업데이트 및 다시 배포

이 단계에서는 게스트 모델을 업데이트하여 각 이벤트 등록에 대한 참석자 수를 추가합니다.

다음 git 명령을 사용하여 *0.2-migration* 릴리스를 확인합니다.

```bash
git checkout tags/0.2-migration
```

이 릴리스에는 보기, 컨트롤러 및 모델에 필요한 변경 내용이 이미 적용되었습니다. 또한 *alembic*(`flask db migrate`)를 통해 생성된 데이터베이스 마이그레이션도 포함됩니다. 다음 git 명령을 통해 적용된 모든 변경 내용을 확인할 수 있습니다.

```bash
git diff 0.1-initialapp 0.2-migration
```

### <a name="test-your-changes-locally"></a>변경 내용을 로컬에서 테스트

다음 명령을 통해 Flask 서버를 로컬로 실행하여 로컬에서 변경 내용을 테스트합니다.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

브라우저에서 http://localhost:5000으로 이동하여 변경 내용을 확인합니다. 테스트 등록을 만듭니다.

![로컬로 실행되는 Docker 컨테이너 기반 Python Flask 응용 프로그램](./media/tutorial-docker-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

새 docker 이미지를 빌드하고 컨테이너 레지스트리에 밀어 넣은 후 앱을 다시 시작합니다.

```bash
cd ..
docker build -t flask-postgresql-sample .
docker tag flask-postgresql-sample <registry_name>.azurecr.io/flask-postgresql-sample
docker push <registry_name>.azurecr.io/flask-postgresql-sample
az appservice web restart --resource-group myResourceGroup --name <app_name>
```

Azure Web App으로 이동하여 새 기능을 다시 시험해 봅니다. 다른 이벤트 등록을 만듭니다.

```bash 
http://<app_name>.azurewebsites.net 
```

![Azure App Service의 Docker Python Flask 앱](./media/tutorial-docker-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-azure-web-app"></a>Azure Web App 관리

[Azure Portal](https://portal.azure.com)로 이동하여 만든 웹앱을 확인합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/tutorial-docker-python-postgresql-app/app-resource.png)

기본적으로 포털에는 웹앱의 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 페이지](./media/tutorial-docker-python-postgresql-app/app-mgmt.png)

## <a name="next-steps"></a>다음 단계

다음 자습서로 이동하여 사용자 지정 DNS 이름을 웹앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](../app-service-web-tutorial-custom-domain.md)
