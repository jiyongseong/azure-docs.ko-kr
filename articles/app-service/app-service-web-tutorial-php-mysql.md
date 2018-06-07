---
title: Azure에서 PHP 및 MySQL 웹앱 빌드 | Microsoft Docs
description: MySQL 데이터베이스에 연결하여 Azure에서 PHP 앱이 작동하도록 하는 방법에 대해 알아봅니다.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 10/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 4bb6f12781666792aad31789a59d752dd5a822de
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2018
---
# <a name="tutorial-build-a-php-and-mysql-web-app-in-azure"></a>자습서: Azure에서 PHP 및 MySQL 웹앱 빌드

> [!NOTE]
> 이 문서에서는 Windows의 App Service에 앱을 배포합니다. _Linux_의 App Service에 배포하려면, [Linux의 Azure App Service에서 PHP와 MySQL 웹앱 빌드](./containers/tutorial-php-mysql-app.md)를 참조하세요.
>

[Azure Web Apps](app-service-web-overview.md)는 확장성 있는 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 Azure에서 PHP 웹앱을 만들고 MySQL 데이터베이스에 연결하는 방법을 보여 줍니다. 완료되면 [Laravel](https://laravel.com/) 앱이 Azure App Service Web Apps에서 실행됩니다.

![Azure App Service에서 실행 중인 PHP 앱](./media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 MySQL 데이터베이스 만들기
> * MySQL에 PHP 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Git 설치](https://git-scm.com/)
* [PHP 5.6.4 이상 설치](http://php.net/downloads.php)
* [작성기 설치](https://getcomposer.org/doc/00-intro.md)
* PHP 확장 Laravel에 필요한 OpenSSL, Pdo-mysql, Mbstring, Tokenizer, XML 사용
* [MySQL 설치 및 시작](https://dev.mysql.com/doc/refman/5.7/en/installing.html) 

## <a name="prepare-local-mysql"></a>로컬 MySQL 준비

이 단계에서는 이 자습서에서 사용할 데이터베이스를 로컬 MySQL 서버에 만듭니다.

### <a name="connect-to-local-mysql-server"></a>로컬 MySQL 서버에 연결

터미널 창에서 로컬 MySQL 서버에 연결합니다. 이 터미널 창을 사용하여 이 자습서의 모든 명령을 실행할 수 있습니다.

```bash
mysql -u root -p
```

암호를 묻는 메시지가 표시되면 `root` 계정에 대한 암호를 입력합니다. 루트 계정 암호를 기억하지 못하는 경우 [MySQL: 루트 암호를 재설정하는 방법](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html)을 참조하세요.

명령이 성공적으로 실행되면 MySQL 서버가 실행되고 있습니다. 그렇지 않은 경우 [MySQL 설치 후 단계](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html)에 따라 로컬 MySQL 서버가 시작되었는지 확인합니다.

### <a name="create-a-database-locally"></a>로컬에서 데이터베이스 만들기

`mysql` 프롬프트에서 데이터베이스를 만듭니다.

```sql 
CREATE DATABASE sampledb;
```

`quit`을 입력하여 서버 연결을 종료합니다.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-php-app-locally"></a>로컬에서 PHP 앱 만들기
이 단계에서는 Laravel 샘플 응용 프로그램 가져오고, 해당 데이터베이스를 구성한 후 로컬로 실행합니다. 

### <a name="clone-the-sample"></a>샘플 복제

터미널 창에서 `cd`를 사용하여 작업 디렉터리로 이동합니다.

다음 명령을 실행하여 샘플 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/laravel-tasks
```

`cd`를 사용하여 복제된 디렉터리로 이동합니다.
필요한 패키지를 설치합니다.

```bash
cd laravel-tasks
composer install
```

### <a name="configure-mysql-connection"></a>MySQL 연결 구성

리포지토리 루트에서 *.env*라는 텍스트 파일을 만듭니다. 다음 변수를 *.env* 파일에 복사합니다. _&lt;root_password>_ 자리 표시자를 MySQL 루트 사용자의 암호로 바꿉니다.

```
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

Laravel에서 _.env_ 파일을 사용하는 방법에 대한 자세한 내용은 [Laravel 환경 구성](https://laravel.com/docs/5.4/configuration#environment-configuration)(영문)을 참조하세요.

### <a name="run-the-sample-locally"></a>로컬에서 샘플 실행

[Laravel 데이터베이스 마이그레이션](https://laravel.com/docs/5.4/migrations)(영문)을 실행하여 응용 프로그램에 필요한 테이블을 만듭니다. 마이그레이션에서 만들어진 테이블을 보려면 Git 리포지토리의 _database/migrations_ 디렉터리를 살펴봅니다.

```bash
php artisan migrate
```

새 Laravel 응용 프로그램 키를 생성합니다.

```bash
php artisan key:generate
```

응용 프로그램을 실행합니다.

```bash
php artisan serve
```

브라우저에서 `http://localhost:8000`으로 이동합니다. 해당 페이지에서 몇 가지 작업을 추가합니다.

![PHP가 MySQL 연결에 성공](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

PHP 서버를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Azure에서 MySQL 만들기

이 단계에서는 [Azure Database for MySQL](/azure/mysql)에서 MySQL 데이터베이스를 만듭니다. 나중에 이 데이터베이스에 연결할 PHP 응용 프로그램을 구성합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-mysql-server"></a>MySQL 서버 만들기

Cloud Shell에서 [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) 명령을 사용하여 Azure Database for MySQL에 서버를 만듭니다.

다음 명령에서 *\<mysql_server_name>* 자리 표시자를 고유한 서버 이름으로, *\<admin_user>* 를 사용자 이름으로, *\<admin_password* 자리 표시자를 암호로 대체하세요. 서버 이름은 PostgreSQL 끝점(`https://<mysql_server_name>.mysql.database.azure.com`)의 일부로 사용되므로 이름은 Azure의 모든 서버에서 고유해야 합니다.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql_server_name> --location "West Europe" --admin-user <admin_user> --admin-password <server_admin_password> --sku-name GP_Gen4_2
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

Cloud Shell에서 [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) 명령을 사용하여 클라이언트 연결을 허용하도록 MySQL 서버에 대한 방화벽 규칙을 만듭니다. 시작 IP 및 끝 IP가 0.0.0.0으로 설정되면 방화벽이 다른 Azure 리소스에 대해서만 열립니다. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> [앱이 사용하는 아웃바운드 IP 주소만 사용](app-service-ip-addresses.md#find-outbound-ips)으로 방화벽 규칙을 훨씬 더 엄격하게 제한할 수 있습니다.
>

### <a name="connect-to-production-mysql-server-locally"></a>로컬에서 프로덕션 MySQL 서버에 연결

로컬 터미널 창에서 Azure의 MySQL 서버에 연결합니다. _&lt;mysql_server_name>_ 에 대해 이전에 지정한 값을 사용합니다. 암호를 묻는 메시지가 표시되면 Azure에서 데이터베이스를 만들 때 지정한 암호를 사용합니다.

```bash
mysql -u <admin_user>@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com-P 3306 -p
```

### <a name="create-a-production-database"></a>프로덕션 데이터베이스 만들기

`mysql` 프롬프트에서 데이터베이스를 만듭니다.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>사용 권한이 있는 사용자 만들기

_phpappuser_라는 데이터베이스 사용자를 만들고 `sampledb` 데이터베이스의 모든 권한을 부여합니다. 다시, 자습서의 편의를 위해 _MySQLAzure2017_을 암호로 사합니다.

```sql
CREATE USER 'phpappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'phpappuser';
```

`quit`를 입력하여 서버 연결을 종료합니다.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Azure MySQL에 앱 연결

이 단계에서는 Azure Database for MySQL에서 만든 MySQL 데이터베이스에 PHP 응용 프로그램을 연결합니다.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>데이터베이스 연결 구성

리포지토리 루트에서 _.env.production_ 파일을 만들고 다음 변수를 복사합니다. *DB_HOST* 및 *DB_USERNAME* 모두에서 자리 표시자 _&lt;mysql_server_name>_ 을 바꿉니다.

```
APP_ENV=production
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=<mysql_server_name>.mysql.database.azure.com
DB_DATABASE=sampledb
DB_USERNAME=phpappuser@<mysql_server_name>
DB_PASSWORD=MySQLAzure2017
MYSQL_SSL=true
```

변경 내용을 저장합니다.

> [!TIP]
> MySQL 연결 정보를 보호하기 위해 이 파일은 이미 Git 리포지토리에서 제외됩니다(리포지토리 루트의 _.gitignore_ 참조). 나중에 Azure Database for MySQL에서 데이터베이스에 연결하도록 App Service에서 환경 변수를 구성하는 방법에 대해 알아봅니다. 환경 변수를 사용하면 App Service에서 *.env* 파일이 필요하지 않습니다.
>

### <a name="configure-ssl-certificate"></a>SSL 인증서 구성

기본적으로 MySQL용 Azure 데이터베이스에는 클라이언트로부터의 SSL 연결이 적용됩니다. Azure의 MySQL 데이터베이스에 연결하려면 [Azure Database for MySQL에서 제공하는 _.pem_ 인증서](../mysql/howto-configure-ssl.md)를 사용해야 합니다.

다음 코드와 같이 _config/database.php_를 열고 `sslmode` 및 `options` 매개 변수를 `connections.mysql`에 추가합니다.

```php
'mysql' => [
    ...
    'sslmode' => env('DB_SSLMODE', 'prefer'),
    'options' => (env('MYSQL_SSL')) ? [
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem', 
    ] : []
],
```

이 자습서에서는 편의를 위해 리포지토리에 `BaltimoreCyberTrustRoot.crt.pem` 인증서가 제공됩니다. 

### <a name="test-the-application-locally"></a>로컬에서 응용 프로그램 테스트

_.env.production_을 환경 파일로 사용해서 Laravel 데이터베이스 마이그레이션을 실행하고 Azure Database for MySQL에서 MySQL 데이터베이스에 테이블을 만듭니다. _.env.production_에는 Azure의 MySQL 데이터베이스에 대한 연결 정보가 있습니다.

```bash
php artisan migrate --env=production --force
```

_.env.production_에는 아직 유효한 응용 프로그램 키가 없습니다. 터미널에서 새 키를 생성합니다.

```bash
php artisan key:generate --env=production --force
```

_.env.production_을 환경 파일로 사용해서 샘플 응용 프로그램을 실행합니다.

```bash
php artisan serve --env=production
```

`http://localhost:8000`로 이동합니다. 오류 없이 페이지가 로드되면 PHP 응용 프로그램이 Azure의 MySQL 데이터베이스에 연결됩니다.

해당 페이지에서 몇 가지 작업을 추가합니다.

![PHP가 Azure Database for MySQL에 성공적으로 연결됨](./media/app-service-web-tutorial-php-mysql/mysql-connect-success.png)

PHP를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

### <a name="commit-your-changes"></a>변경 내용을 커밋합니다

다음 Git 명령을 실행하여 변경 내용을 커밋합니다.

```bash
git add .
git commit -m "database.php updates"
```

앱을 배포할 준비가 되었습니다.

## <a name="deploy-to-azure"></a>Deploy to Azure

이 단계에서는 MySQL에 연결된 PHP 응용 프로그램을 Azure App Service에 배포합니다.

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service 계획 만들기

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>웹앱 만들기

[!INCLUDE [Create web app no h](../../includes/app-service-web-create-web-app-php-no-h.md)] 

### <a name="configure-database-settings"></a>데이터베이스 설정 구성

앞서 설명한 것처럼 App Service에서 환경 변수를 사용하여 Azure MySQL 데이터베이스에 연결할 수 있습니다.

Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 명령을 사용하여 환경 변수를 _앱 설정_으로 설정합니다.

다음 명령에서는 `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` 및 `DB_PASSWORD` 앱 설정을 구성합니다. _&lt;appname>_ 및 _&lt;mysql_server_name>_ 자리 표시자를 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="phpappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017" MYSQL_SSL="true"
```

PHP [getenv](http://www.php.net/manual/function.getenv.php) 메서드를 사용하여 설정에 액세스할 수 있습니다. Laravel 코드에서는 `getenv` PHP에 대해 [env()](https://laravel.com/docs/5.4/helpers#method-env) 래퍼를 사용합니다. 예를 들어 _config/database.php_의 MySQL 구성은 다음 코드와 같습니다.

```php
'mysql' => [
    'driver'    => 'mysql',
    'host'      => env('DB_HOST', 'localhost'),
    'database'  => env('DB_DATABASE', 'forge'),
    'username'  => env('DB_USERNAME', 'forge'),
    'password'  => env('DB_PASSWORD', ''),
    ...
],
```

### <a name="configure-laravel-environment-variables"></a>Laravel 환경 변수 구성

Laravel에는 App Service의 응용 프로그램 키가 필요합니다. 앱 설정을 사용하여 키를 구성할 수 있습니다.

로컬 터미널 창에서 `php artisan`을 사용하여 _.env_로 저장하지 않으면서 새 응용 프로그램 키를 생성합니다.

```bash
php artisan key:generate --show
```

Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 명령을 사용하여 App Service 웹앱에서 응용 프로그램 키를 설정합니다. 자리 표시자 _&lt;appname>_ 및 _&lt;outputofphpartisankey:generate>_ 를 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
```

`APP_DEBUG="true"`는 배포된 웹앱에서 오류가 발생하면 디버깅 정보를 반환하도록 Laravel에 지시합니다. 프로덕션 응용 프로그램을 실행할 때 더 안전한 `false`로 설정합니다.

### <a name="set-the-virtual-application-path"></a>가상 응용 프로그램 경로 설정

웹앱에 대한 가상 응용 프로그램 경로를 설정합니다. [Laravel 응용 프로그램 수명 주기](https://laravel.com/docs/5.4/lifecycle)(영문)가 응용 프로그램의 루트 디렉터리 대신 _public_ 디렉터리에서 시작되므로 이 단계가 필요합니다. 해당 수명 주기가 루트 디렉터리에서 시작하는 다른 PHP 프레임워크는 가상 응용 프로그램 경로를 수동으로 구성하지 않아도 작동될 수 있습니다.

Cloud Shell에서 [`az resource update`](/cli/azure/resource#az_resource_update) 명령을 사용하여 가상 응용 프로그램 경로를 설정합니다. _&lt;appname>_ 자리 표시자를 바꿉니다.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

기본적으로 Azure App Service에서는 루트 가상 응용 프로그램 경로(_/_)가 배포된 응용 프로그램 파일의 루트 디렉터리(_sites\wwwroot_)를 가리킵니다.

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

> [!NOTE]
> 배포 프로세스를 진행하면 마지막에 [Composer](https://getcomposer.org/) 패키지가 설치되는 것을 알 수 있습니다. App Service는 기본 배포 중에 이러한 자동화를 실행하지 않으므로 이 샘플 리포지토리는 사용 설정에 사용되는 추가 파일 3개가 루트 디렉터리에 들어 있습니다.
>
> - `.deployment` - 이 파일은 App Service에서 `bash deploy.sh`를 사용자 지정 배포 스크립트로 실행하게 만듭니다.
> - `deploy.sh` - 사용자 지정 배포 스크립트입니다. 파일을 검토하는 경우 `npm install` 다음에 `php composer.phar install`이 실행되는 것을 볼 수 있습니다.
> - `composer.phar` - Composer 패키지 관리자입니다.
>
> 이 방식으로 App Service에 대한 Git 기반 배포에 어떤 단계든 추가할 수 있습니다. 자세한 내용은 [사용자 지정 배포 스크립트](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)를 참조하세요.
>

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기

`http://<app_name>.azurewebsites.net`으로 이동한 후 목록에 몇 가지 작업을 추가합니다.

![Azure App Service에서 실행 중인 PHP 앱](./media/app-service-web-tutorial-php-mysql/php-mysql-in-azure.png)

축하합니다! Azure App Service에서 데이터 기반 PHP 앱을 실행하고 있습니다.

## <a name="update-model-locally-and-redeploy"></a>로컬에서 모델 업데이트 및 다시 배포

이 단계에서는 `task` 데이터 모델과 웹앱을 간단히 변경한 다음 업데이트를 Azure에 게시합니다.

작업 시나리오의 경우 작업을 완료한 것으로 표시할 수 있도록 응용 프로그램을 수정합니다.

### <a name="add-a-column"></a>열 추가

로컬 터미널 창에서 Git 리포지토리의 루트로 이동합니다.

`tasks` 테이블에 대한 새 데이터베이스 마이그레이션을 생성합니다.

```bash
php artisan make:migration add_complete_column --table=tasks
```

이 명령은 생성되는 마이그레이션 파일의 이름을 표시합니다. _database/migrations_에서 이 파일을 찾아서 엽니다.

`up` 메서드를 다음 코드로 바꿉니다.

```php
public function up()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->boolean('complete')->default(False);
    });
}
```

앞의 코드는 `complete`라는 `tasks` 테이블에 부울 열을 추가합니다.

`down` 메서드를 롤백 작업에 대한 다음 코드로 바꿉니다.

```php
public function down()
{
    Schema::table('tasks', function (Blueprint $table) {
        $table->dropColumn('complete');
    });
}
```

로컬 터미널 창에서 Laravel 데이터베이스 마이그레이션을 실행하여 로컬 데이터베이스를 변경합니다.

```bash
php artisan migrate
```

[Laravel 명명 규칙](https://laravel.com/docs/5.4/eloquent#defining-models)에 따라 `Task`(_app/Task.php_ 참조) 모델은 기본적으로 `tasks` 테이블에 매핑됩니다.

### <a name="update-application-logic"></a>응용 프로그램 논리 업데이트

*routes/web.php* 파일을 엽니다. 응용 프로그램에서 해당 경로 및 비즈니스 논리를 여기에 정의합니다.

파일의 끝에 다음 코드를 포함하는 경로를 추가합니다.

```php
/**
 * Toggle Task completeness
 */
Route::post('/task/{id}', function ($id) {
    error_log('INFO: post /task/'.$id);
    $task = Task::findOrFail($id);

    $task->complete = !$task->complete;
    $task->save();

    return redirect('/');
});
```

앞의 코드는 `complete` 값을 설정/해제하여 데이터 모델을 간단히 업데이트합니다.

### <a name="update-the-view"></a>보기 업데이트

*resources/views/tasks.blade.php* 파일을 엽니다. `<tr>` 여는 태그를 찾아 다음으로 바꿉니다.

```html
<tr class="{{ $task->complete ? 'success' : 'active' }}" >
```

앞의 코드는 작업이 완료되었는지 여부에 따라 행 색을 변경합니다.

다음 줄에는 다음 코드가 있습니다.

```html
<td class="table-text"><div>{{ $task->name }}</div></td>
```

전체 줄을 다음 코드로 바꿉니다.

```html
<td>
    <form action="{{ url('task/'.$task->id) }}" method="POST">
        {{ csrf_field() }}

        <button type="submit" class="btn btn-xs">
            <i class="fa {{$task->complete ? 'fa-check-square-o' : 'fa-square-o'}}"></i>
        </button>
        {{ $task->name }}
    </form>
</td>
```

앞의 코드는 이전에 정의한 경로를 참조하는 제출 단추를 추가합니다.

### <a name="test-the-changes-locally"></a>로컬에서 변경 내용 테스트

로컬 터미널 창에서 Git 리포지토리의 루트 디렉터리에서 개발 서버를 실행합니다.

```bash
php artisan serve
```

작업 상태 변경을 확인하려면 `http://localhost:8000`으로 이동하고 확인란을 선택합니다.

![작업에 확인란이 추가됨](./media/app-service-web-tutorial-php-mysql/complete-checkbox.png)

PHP를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

로컬 터미널 창에서 프로덕션 연결 문자열로 Laravel 데이터베이스 마이그레이션을 실행하여 Azure 데이터베이스를 변경합니다.

```bash
php artisan migrate --env=production --force
```

Git에서 모든 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

`git push`가 완료되면 Azure 웹앱으로 이동하여 새 기능을 테스트해 봅니다.

![Azure에 게시된 모델 및 데이터베이스 변경 내용](media/app-service-web-tutorial-php-mysql/complete-checkbox-published.png)

모든 작업을 추가했으면 데이터베이스에서 유지됩니다. 데이터 스키마를 업데이트하는 경우 기존 데이터는 그대로 유지됩니다.

## <a name="stream-diagnostic-logs"></a>진단 로그 스트림

PHP 응용 프로그램이 Azure App Service에서 실행되는 동안 콘솔 로그를 터미널에 파이프할 수 있습니다. 이 방법으로 응용 프로그램 오류를 디버깅하는 데 도움이 되는 진단 메시지를 동일하게 받을 수 있습니다.

로그 스트리밍을 시작하려면 Cloud Shell에서 [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az_webapp_log_tail) 명령을 사용합니다.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
```

로그 스트리밍이 시작되면 브라우저에서 Azure 웹앱을 새로 고쳐 일부 웹 트래픽을 가져옵니다. 이제 터미널에 파이프된 콘솔 로그가 표시될 수 있습니다. 콘솔 로그가 즉시 표시되지 않으면 30초 후에 다시 확인합니다.

언제든지 로그 스트리밍을 중지하려면 `Ctrl`+`C`를 입력합니다.

> [!TIP]
> PHP 응용 프로그램은 표준 [error_log()](http://php.net/manual/function.error-log.php)를 사용하여 콘솔에 출력할 수 있습니다. 샘플 응용 프로그램에서 _app/Http/routes.php_에서 이 접근 방식을 사용합니다.
>
> 웹 프레임워크로서 [Laravel은 Monolog](https://laravel.com/docs/5.4/errors) 로깅 공급자로 사용합니다. Monolog에서 콘솔에 메시지를 출력하게 하는 방법을 보려면 [PHP: monolog를 사용하여 콘솔에 로깅하는 방법(php://out)](http://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out)을 참조하세요.
>
>

## <a name="manage-the-azure-web-app"></a>Azure 웹앱 관리

만든 웹앱을 관리하려면 [Azure Portal](https://portal.azure.com)로 이동합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/app-service-web-tutorial-php-mysql/access-portal.png)

웹앱의 개요 페이지가 표시됩니다. 여기서 중지, 시작, 다시 시작, 찾아보기 및 삭제와 같은 기본 관리 작업을 수행할 수 있습니다.

왼쪽 메뉴에서 앱을 구성하기 위한 페이지를 제공합니다.

![Azure Portal의 App Service 페이지](./media/app-service-web-tutorial-php-mysql/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure에서 MySQL 데이터베이스 만들기
> * MySQL에 PHP 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

다음 자습서로 이동하여 사용자 지정 DNS 이름을 웹앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)
