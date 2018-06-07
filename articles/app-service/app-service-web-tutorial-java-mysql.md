---
title: Azure에서 Java 및 MySQL 웹앱 빌드
description: Azure MySQL 데이터베이스 서비스에 연결되는 Java 앱이 Azure App Service에서 작동되도록 하는 방법을 알아봅니다.
services: app-service\web
documentationcenter: Java
author: bbenz
manager: jeffsand
editor: jasonwhowell
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 05/22/2017
ms.author: bbenz
ms.custom: mvc
ms.openlocfilehash: 46e222ffe40db186343250efc71e20d41adbc285
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="tutorial-build-a-java-and-mysql-web-app-in-azure"></a>자습서: Azure에서 Java 및 MySQL 웹앱 빌드

> [!NOTE]
> 이 문서에서는 Windows의 App Service에 앱을 배포합니다. _Linux_의 App Service에 배포하려면 [Azure에 컨테이너화된 Spring Boot 앱 배포](/java/azure/spring-framework/deploy-containerized-spring-boot-java-app-with-maven-plugin)를 참조하세요.
>

이 자습서에서는 Azure에서 Java 웹앱을 만들고 MySQL 데이터베이스에 연결하는 방법을 보여 줍니다. 작업이 완료되면 [Azure App Service Web Apps](app-service-web-overview.md)에서 실행되는 [MySQL용 Azure 데이터베이스](https://docs.microsoft.com/azure/mysql/overview)에 [Spring Boot](https://projects.spring.io/spring-boot/) 응용 프로그램 저장 데이터가 생깁니다.

![Azure App Service에서 실행 중인 Java 앱](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 MySQL 데이터베이스 만들기
> * 데이터베이스에 샘플 앱 연결
> * Azure에 앱 배포
> * 앱 업데이트 및 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 모니터링

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

1. [Git 다운로드 및 설치](https://git-scm.com/)
1. [JDK Java 7 이상 다운로드 및 설치](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
1. [MySQL 다운로드, 설치 및 시작](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>로컬 MySQL 준비 

이 단계에서는 컴퓨터에서 로컬로 앱을 테스트하는 데 사용할 데이터베이스를 로컬 MySQL 서버에 만듭니다.

### <a name="connect-to-mysql-server"></a>MySQL 서버에 연결

터미널 창에서 로컬 MySQL 서버에 연결합니다. 이 터미널 창을 사용하여 이 자습서의 모든 명령을 실행할 수 있습니다.

```bash
mysql -u root -p
```

암호를 묻는 메시지가 표시되면 `root` 계정에 대한 암호를 입력합니다. 루트 계정 암호를 기억하지 못하는 경우 [MySQL: 루트 암호를 재설정하는 방법](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)을 참조하세요.

명령이 성공적으로 실행되면 MySQL 서버가 이미 실행되고 있는 것입니다. 그렇지 않은 경우 [MySQL 설치 후 단계](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)에 따라 로컬 MySQL 서버가 시작되었는지 확인합니다.

### <a name="create-a-database"></a>데이터베이스 만들기 

`mysql` 프롬프트에서 할 일 항목에 대한 데이터베이스 및 테이블을 만듭니다.

```sql
CREATE DATABASE tododb;
```

`quit`을 입력하여 서버 연결을 종료합니다.

```sql
quit
```

## <a name="create-and-run-the-sample-app"></a>샘플 앱 만들기 및 실행 

이 단계에서는 샘플 Spring 부팅 앱을 복제하고 로컬 MySQL 데이터베이스를 사용하도록 구성한 다음 컴퓨터에서 실행합니다. 

### <a name="clone-the-sample"></a>샘플 복제

터미널 창에서 작업 디렉터리로 이동하고 샘플 리포지토리를 복제합니다. 

```bash
git clone https://github.com/azure-samples/mysql-spring-boot-todo
```

### <a name="configure-the-app-to-use-the-mysql-database"></a>MySQL 데이터베이스를 사용하도록 앱 구성

MySQL 프롬프트를 여는 데 사용한 것과 동일한 루트 암호로 *spring-boot-mysql-todo/src/main/resources/application.properties*의 `spring.datasource.password` 및 값을 업데이트합니다.

```
spring.datasource.password=mysqlpass
```

### <a name="build-and-run-the-sample"></a>샘플 빌드 및 실행

리포지토리에 포함된 Maven 래퍼를 사용하여 샘플을 빌드하고 실행합니다.

```bash
cd spring-boot-mysql-todo
mvnw package spring-boot:run
```

브라우저를 열어 `http://localhost:8080`에 접속하여 작업의 샘플에서 봅니다. 목록에 작업을 추가하여 MySQL 프롬프트에서 다음 SQL 명령을 사용하여 MySQL에 저장된 데이터를 봅니다.

```SQL
use testdb;
select * from todo_item;
```

터미널에서 `Ctrl`+`C`를 입력하여 응용 프로그램을 중지합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-mysql-database"></a>Azure MySQL 데이트베이스 만들기

이 단계에서는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 사용하여 [MySQL용 Azure 데이터베이스 ](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md) 인스턴스를 만듭니다. 이 자습서의 후반부에서 샘플 응용 프로그램을 구성하여 이 데이터베이스를 사용합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[`az group create`](/cli/azure/group#az_group_create) 명령을 실행하여 [리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. Azure 리소스 그룹은 웹앱, 데이터베이스, 저장소 계정 등의 관련 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예wp에서는 북유럽 지역의 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location "North Europe"
```    

`--location`에 사용할 수 있는 가능한 값을 보려면 [`az appservice list-locations`](/cli/azure/appservice#list-locations) 명령을 사용합니다.

### <a name="create-a-mysql-server"></a>MySQL 서버 만들기

Cloud Shell에서 [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) 명령을 사용하여 Azure Database for MySQL에 서버를 만듭니다.

다음 명령에서 *\<mysql_server_name>* 자리 표시자를 고유한 서버 이름으로, *\<admin_user>* 를 사용자 이름으로, *\<admin_password* 자리 표시자를 암호로 대체하세요. 서버 이름은 PostgreSQL 끝점(`https://<mysql_server_name>.mysql.database.azure.com`)의 일부로 사용되므로 이름은 Azure의 모든 서버에서 고유해야 합니다.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name>--location "West Europe" --admin-user <admin_user> --admin-password <server_admin_password> --sku-name GP_Gen4_2
```

> [!NOTE]
> 이 자습서에서 고려하는 자격 증명에는 여러 가지가 있으므로, 혼동을 피하기 위해 `--admin-user` 및 `--admin-password`는 dummy 값으로 설정됩니다. 프로덕션 환경에서 Azure의 MySQL 서버에 사용할 좋은 사용자 이름 및 암호를 선택하는 경우 보안 모범 사례를 따릅니다.
>
>

MySQL 서버를 만들면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "location": "westeurope",
  "name": "<mysql_server_name>",
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

Cloud Shell에서 [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create) 명령을 사용하여 클라이언트 연결을 허용하도록 MySQL 서버에 대한 방화벽 규칙을 만듭니다. 시작 IP 및 끝 IP가 0.0.0.0으로 설정되면 방화벽이 다른 Azure 리소스에 대해서만 열립니다. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> [앱이 사용하는 아웃바운드 IP 주소만 사용](app-service-ip-addresses.md#find-outbound-ips)으로 방화벽 규칙을 훨씬 더 엄격하게 제한할 수 있습니다.
>

## <a name="configure-the-azure-mysql-database"></a>Azure MySQL 데이터베이스 구성

로컬 터미널 창에서 Azure의 MySQL 서버에 연결합니다. _&lt;mysql_server_name>_ 에 대해 이전에 지정한 값을 사용합니다. 암호를 묻는 메시지가 표시되면 Azure에서 데이터베이스를 만들 때 지정한 암호를 사용합니다.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

### <a name="create-a-database"></a>데이터베이스 만들기 

`mysql` 프롬프트에서 할 일 항목에 대한 데이터베이스 및 테이블을 만듭니다.

```sql
CREATE DATABASE tododb;
```

### <a name="create-a-user-with-permissions"></a>사용 권한이 있는 사용자 만들기

데이터베이스 사용자를 만들고 `tododb` 데이터베이스에서 모든 권한을 부여합니다. 자리 표시자 `<Javaapp_user>` 및 `<Javaapp_password>`를 고유한 응용 프로그램 이름으로 바꿉니다.

```sql
CREATE USER '<Javaapp_user>' IDENTIFIED BY '<Javaapp_password>'; 
GRANT ALL PRIVILEGES ON tododb.* TO '<Javaapp_user>';
```

`quit`을 입력하여 서버 연결을 종료합니다.

```sql
quit
```

## <a name="deploy-the-sample-to-azure-app-service"></a>Azure App Service에 샘플 배포

[`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) CLI 명령을 사용하여 **무료** 가격 책정 계층으로 Azure App Service 계획을 만듭니다. App Service 계획은 앱을 호스트하는 데 사용되는 실제 리소스를 정의합니다. App Service 계획에 할당된 모든 응용 프로그램은 이들 리소스를 공유하므로 여러 앱을 호스팅할 때 비용을 절감할 수 있습니다. 

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

계획이 준비되면 Azure CLI는 다음 예와 비슷한 출력이 표시됩니다.

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Azure Web App 만들기

Cloud Shell에서 [`az webapp create`](/cli/azure/appservice/web#az_appservice_web_create) CLI 명령을 사용하여 `myAppServicePlan` App Service 계획에서 웹앱 정의를 만듭니다. 웹앱 정의는 응용 프로그램에 액세스하는 URL을 제공하고 Azure에 코드를 배포하는 몇 가지 옵션을 구성합니다. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan
```

`<app_name>` 자리 표시자를 고유한 앱 이름으로 대체합니다. 이 고유한 이름은 웹앱에 대한 기본 도메인 이름의 일부이므로 이름은 Azure에 있는 모든 앱에서 고유해야 합니다. 사용자에게 노출하기 전에 웹앱에 사용자 지정 도메인 이름 항목을 매핑할 수 있습니다.

웹앱 정의가 준비되면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다. 

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

### <a name="configure-java"></a>Java 구성 

Cloud Shell에서 [`az webapp config set`](/cli/azure/webapp/config#az_webapp_config_set) 명령으로 앱에 필요한 Java 런타임 구성을 설정합니다.

다음 명령은 최근 Java 8 JDK 및 [Apache Tomcat](http://tomcat.apache.org/) 8.0에서 실행되도록 웹앱을 구성합니다.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --java-version 1.8 --java-container Tomcat --java-container-version 8.0
```

### <a name="configure-the-app-to-use-the-azure-sql-database"></a>Azure SQL Database를 사용하도록 앱 구성

샘플 앱을 실행하기 전에 Azure에서 만든 Azure MySQL 데이터베이스를 사용하기 위해 웹앱에서 응용 프로그램 설정을 지정합니다. 이러한 속성은 환경 변수로서 웹 응용 프로그램에 노출되며 패키지된 웹앱 내부의 application.properties에 설정된 값을 재정의합니다. 

Cloud Shell에서 CLI의 [`az webapp config appsettings`](https://docs.microsoft.com/cli/azure/appservice/web/config/appsettings)를 사용하여 응용 프로그램 설정을 지정합니다.

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_URL="jdbc:mysql://<mysql_server_name>.mysql.database.azure.com:3306/tododb?verifyServerCertificate=true&useSSL=true&requireSSL=false" --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_USERNAME=Javaapp_user@mysql_server_name --resource-group myResourceGroup --name <app_name>
```

```azurecli-interactive
az webapp config appsettings set --settings SPRING_DATASOURCE_PASSWORD=Javaapp_password --resource-group myResourceGroup --name <app_name>
```

### <a name="get-ftp-deployment-credentials"></a>FTP 배포 자격 증명 가져오기 
FTP, 로컬 Git, GitHub, Visual Studio Team Services 및 BitBucket과 같은 다양한 방법으로 응용 프로그램을 Azure App Service에 배포할 수 있습니다. 이 예에서는 로컬 컴퓨터에 이전에 빌드된 .WAR 파일을 Azure App Service에 배포하는 FTP입니다.

ftp 명령에서 웹앱으로 전달할 자격 증명을 결정하려면 Cloud Shell에서 [`az appservice web deployment list-publishing-profiles`](https://docs.microsoft.com/cli/azure/appservice/web/deployment#az_appservice_web_deployment_list_publishing_profiles) 명령을 사용합니다. 

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app_name> --resource-group myResourceGroup --query "[?publishMethod=='FTP'].{URL:publishUrl, Username:userName,Password:userPWD}" --output json
```

```JSON
[
  {
    "Password": "aBcDeFgHiJkLmNoPqRsTuVwXyZ",
    "URL": "ftp://waws-prod-blu-069.ftp.azurewebsites.windows.net/site/wwwroot",
    "Username": "app_name\\$app_name"
  }
]
```

### <a name="upload-the-app-using-ftp"></a>FTP를 사용하여 앱 업로드

선호하는 FTP 도구를 사용하여 이전 명령의 `URL` 필드에서 가져온 서버 주소에 있는 */site/wwwroot/webapps* 폴더에 .WAR 파일을 배포합니다. 기존 기본(루트) 응용 프로그램 디렉터리를 제거하고 기존 ROOT.war를 자습서의 앞부분에서 빌드한 .WAR 파일로 대체합니다.

```bash
ftp waws-prod-blu-069.ftp.azurewebsites.windows.net
Connected to waws-prod-blu-069.drip.azurewebsites.windows.net.
220 Microsoft FTP Service
Name (waws-prod-blu-069.ftp.azurewebsites.windows.net:raisa): app_name\$app_name
331 Password required
Password:
cd /site/wwwroot/webapps
mdelete -i ROOT/*
rmdir ROOT/
put target/TodoDemo-0.0.1-SNAPSHOT.war ROOT.war
```

### <a name="test-the-web-app"></a>웹앱 테스트

`http://<app_name>.azurewebsites.net/`으로 이동한 후 목록에 몇 가지 작업을 추가합니다. 

![Azure App Service에서 실행 중인 Java 앱](./media/app-service-web-tutorial-java-mysql/appservice-web-app.png)

**축하합니다.** Azure App Service에서 데이터 기반 Java 앱이 실행되고 있습니다.

## <a name="update-the-app-and-redeploy"></a>앱 업데이트 및 다시 배포

항목을 만든 날에 대한 할일 목록에 추가 열을 포함하도록 응용 프로그램을 업데이트합니다. 기존 데이터베이스 레코드를 변경하지 않고 데이터 모델이 변경되면 Spring Boot는 사용자에 대한 데이터베이스 스키마 업데이트를 처리합니다.

1. 로컬 시스템에서 *src/main/java/com/example/fabrikam/TodoItem.java*를 열고 다음 가져오기를 클래스에 추가합니다.   

    ```java
    import java.text.SimpleDateFormat;
    import java.util.Calendar;
    ```

2. `String` 속성 `timeCreated`를 *src/main/java/com/example/fabrikam/TodoItem.java*에 추가하여 개체 생성 시 timestamp으로 초기화합니다. 이 파일을 편집하는 동안 새 `timeCreated` 속성에 대해 getter/setter를 추가합니다.

    ```java
    private String name;
    private boolean complete;
    private String timeCreated;
    ...

    public TodoItem(String category, String name) {
       this.category = category;
       this.name = name;
       this.complete = false;
       this.timeCreated = new SimpleDateFormat("MMMM dd, YYYY").format(Calendar.getInstance().getTime());
    }
    ...
    public void setTimeCreated(String timeCreated) {
       this.timeCreated = timeCreated;
    }

    public String getTimeCreated() {
        return timeCreated;
    }
    ```

3. *src/main/java/com/example/fabrikam/TodoDemoController.java*를 `updateTodo` 메서드의 라인으로 업데이트하여 timestamp를 설정합니다.

    ```java
    item.setComplete(requestItem.isComplete());
    item.setId(requestItem.getId());
    item.setTimeCreated(requestItem.getTimeCreated());
    repository.save(item);
    ```

4. `Thymeleaf` 템플릿에서 새 필드에 대한 지원을 추가합니다. *src/main/resources/templates/index.html*를 timestamp에 대한 새 테이블 머리글, 그리고 각 테이블의 데이터 행의 timestamp의 값을 표시하는 새 필드로 업데이트합니다.

    ```html
    <th>Name</th>
    <th>Category</th>
    <th>Time Created</th>
    <th>Complete</th>
    ...
    <td th:text="${item.category}">item_category</td><input type="hidden" th:field="*{todoList[__${i.index}__].category}"/>
    <td th:text="${item.timeCreated}">item_time_created</td><input type="hidden" th:field="*{todoList[__${i.index}__].timeCreated}"/>
    <td><input type="checkbox" th:checked="${item.complete} == true" th:field="*{todoList[__${i.index}__].complete}"/></td>
    ```

5. 응용 프로그램을 다시 빌드합니다.

    ```bash
    mvnw clean package 
    ```

6. FTP는 이전과 같은 업데이트된 .WAR이며, 기존 *site/wwwroot/webapps/ROOT* 디렉터리와 *ROOT.war*를 제거한 다음, 업데이트된 .WAR 파일을 ROOT.war로 업로드합니다. 

앱을 새로 고치면 **만든 시간** 열이 표시됩니다. 새 작업을 추가하면 앱은 자동으로 timestamp를 채웁니다. 기본 데이터 모델이 변경되더라도 기존 작업은 변경되지 않고 앱와 함께 작동합니다. 

![새 열로 업데이트된 Java 앱](./media/app-service-web-tutorial-java-mysql/appservice-updates-java.png)
      
## <a name="stream-diagnostic-logs"></a>진단 로그 스트림 

Java 응용 프로그램을 Azure App Service에서 실행하는 동안 콘솔 로그를 바로 터미널에 보낼 수 있습니다. 이 방법으로 응용 프로그램 오류를 디버깅하는 데 도움이 되는 진단 메시지를 동일하게 받을 수 있습니다.

로그 스트리밍을 시작하려면 Cloud Shell에서 [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) 명령을 사용합니다.

```azurecli-interactive 
az webapp log tail --name <app_name> --resource-group myResourceGroup 
``` 

## <a name="manage-your-azure-web-app"></a>Azure Web App 관리

[Azure Portal](https://portal.azure.com)로 이동하여 만든 웹앱을 확인합니다.

왼쪽 메뉴에서 **App Service**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/app-service-web-tutorial-java-mysql/access-portal.png)

기본적으로 웹앱 페이지에는 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 중지, 시작, 다시 시작, 삭제와 같은 관리 작업을 수행할 수 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 페이지](./media/app-service-web-tutorial-java-mysql/web-app-blade.png)

이 페이지의 이러한 탭은 웹앱에 추가할 수 있는 유용한 많은 기능을 보여 줍니다. 다음은 몇 가지 가능성을 제공합니다.
* 사용자 지정 DNS 이름 매핑
* 사용자 지정 SSL 인증서 바인딩
* 지속적 배포 구성
* 수평 및 수직 확장
* 사용자 인증 추가

## <a name="clean-up-resources"></a>리소스 정리

다른 자습서에서 이러한 리소스가 필요하지 않으면([다음 단계](#next) 참조) Cloud Shell에서 다음 명령을 실행하여 삭제할 수 있습니다. 
  
```azurecli-interactive
az group delete --name myResourceGroup 
``` 

<a name="next"></a>

## <a name="next-steps"></a>다음 단계

> [!div class="checklist"]
> * Azure에서 MySQL 데이터베이스 만들기
> * MySQL에 샘플 Java 앱 연결
> * Azure에 앱 배포
> * 앱 업데이트 및 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

사용자 지정 DNS 이름을 앱에 매핑하는 방법에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"] 
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)
