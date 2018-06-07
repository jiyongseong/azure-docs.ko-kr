---
title: Linux의 Azure App Service에서 Ruby 및 MySQL 웹앱 작성 | Microsoft Docs
description: MySQL 데이터베이스에 연결하여 Azure에서 Ruby 앱이 작동하도록 하는 방법에 대해 알아봅니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 12/21/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: a026eafeb71c67a2cb98c20c4fc5af16073be083
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="build-a-ruby-and-mysql-web-app-in-azure-app-service-on-linux"></a>Linux의 Azure App Service에서 Ruby 및 MySQL 웹앱 작성

[Linux의 App Service](app-service-linux-intro.md)는 Linux 운영 체제를 사용하여 확장성이 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 Ruby 웹앱을 만들고 MySQL 데이터베이스에 연결하는 방법을 보여줍니다. 완료되면 [Ruby on Rails](http://rubyonrails.org/) 앱이 Linux의 App Service에서 실행됩니다.

![Azure App Service에서 실행 중인 Ruby on Rails 앱](./media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure에서 MySQL 데이터베이스 만들기
> * Ruby on Rails 앱을 MySQL에 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Git 설치](https://git-scm.com/)
* [Ruby 2.3 설치](https://www.ruby-lang.org/documentation/installation/)
* [Ruby on Rails 5.1 설치](http://guides.rubyonrails.org/v5.1/getting_started.html)
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

`quit`을 입력하여 서버 연결을 종료합니다.

```sql
quit
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>로컬로 Ruby on Rails 앱 만들기
이 단계에서는 Ruby on Rails 샘플 응용 프로그램 가져오고, 해당 데이터베이스를 구성한 후 로컬로 실행합니다. 

### <a name="clone-the-sample"></a>샘플 복제

터미널 창에서 `cd`를 사용하여 작업 디렉터리로 이동합니다.

다음 명령을 실행하여 샘플 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

`cd`를 사용하여 복제된 디렉터리로 이동합니다. 필요한 패키지를 설치합니다.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="configure-mysql-connection"></a>MySQL 연결 구성

리포지토리에서 _config/database.yml_을 열고 로컬 MySQL 루트 사용자 이름 및 암호를 제공합니다(줄 13). [Homebrew](https://brew.sh/)와 같은 도구를 사용하여 MySQL을 설치한 경우 파일의 자격 증명이 이미 기본값으로 설정되어 있습니다(`root` 및 빈 암호).

```txt
default: &default
  adapter: mysql2
  encoding: utf8
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: root
  password:
  socket: /tmp/mysql.sock
```

### <a name="run-the-sample-locally"></a>로컬에서 샘플 실행

[Rails 마이그레이션](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations)을 실행하여 응용 프로그램에 필요한 테이블을 만듭니다. 마이그레이션에서 만들어진 테이블을 보려면 Git 리포지토리의 _db/migrate_ 디렉터리를 살펴봅니다.

```bash
rake db:create
rake db:migrate
```

응용 프로그램을 실행합니다.

```bash
rails server
```

브라우저에서 `http://localhost:3000`으로 이동합니다. 해당 페이지에서 몇 가지 작업을 추가합니다.

![Ruby on Rails가 MySQL에 성공적으로 연결됩니다.](./media/tutorial-ruby-mysql-app/mysql-connect-success.png)

Rails 서버를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-mysql-in-azure"></a>Azure에서 MySQL 만들기

이 단계에서는 [MySQL용 Azure 데이터베이스(미리 보기)](/azure/mysql)에서 MySQL 데이터베이스를 만듭니다. 나중에 이 데이터베이스에 연결할 Ruby on Rails 응용 프로그램을 구성합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-mysql-server"></a>MySQL 서버 만들기

[`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create) 명령을 사용하여 Azure Database for MySQL(미리 보기)의 서버를 만듭니다.

다음 명령에서 _&lt;mysql_server_name>_ 자리 표시자를 고유한 MySQL 서버 이름으로 바꿉니다(유효한 문자: `a-z`, `0-9` 및 `-`). 이 이름은 MySQL 서버의 호스트 이름(`<mysql_server_name>.mysql.database.azure.com`)의 일부이며, 전역적으로 고유해야 합니다.

```azurecli-interactive
az mysql server create --name <mysql_server_name> --resource-group myResourceGroup --location "North Europe" --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd!
```

MySQL 서버를 만들면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql_server_name>.database.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql_server_name>",
  "location": "northeurope",
  "name": "<mysql_server_name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>서버 방화벽 구성

[`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az_mysql_server_firewall_rule_create) 명령을 사용하여 클라이언트 연결을 허용하도록 MySQL 서버에 대한 방화벽 규칙을 만듭니다. 시작 IP 및 끝 IP가 0.0.0.0으로 설정되면 방화벽이 다른 Azure 리소스에 대해서만 열립니다. 

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql_server_name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> [앱이 사용하는 아웃바운드 IP 주소만 사용](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips)으로 방화벽 규칙을 훨씬 더 엄격하게 제한할 수 있습니다.
>

### <a name="connect-to-production-mysql-server-locally"></a>로컬에서 프로덕션 MySQL 서버에 연결

터미널 창에서 Azure의 MySQL 서버에 연결합니다. _&lt;mysql_server_name>_ 에 대해 이전에 지정한 값을 사용합니다.

```bash
mysql -u adminuser@<mysql_server_name> -h <mysql_server_name>.mysql.database.azure.com -P 3306 -p
```

암호를 묻는 메시지가 나타나면 데이터베이스 서버를 만들 때 지정한 _MY5up3r$tr0ngPa$w0rd!_ 를 사용합니다.

### <a name="create-a-production-database"></a>프로덕션 데이터베이스 만들기

`mysql` 프롬프트에서 데이터베이스를 만듭니다.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>사용 권한이 있는 사용자 만들기

_railsappuser_라는 데이터베이스 사용자를 만들고 `sampledb` 데이터베이스의 모든 권한을 부여합니다.

```sql
CREATE USER 'railsappuser' IDENTIFIED BY 'MySQLAzure2017'; 
GRANT ALL PRIVILEGES ON sampledb.* TO 'railsappuser';
```

`quit`를 입력하여 서버 연결을 종료합니다.

```sql
quit
```

## <a name="connect-app-to-azure-mysql"></a>Azure MySQL에 앱 연결

이 단계에서는 MySQL용 Azure 데이터베이스(미리 보기)에서 만든 MySQL 데이터베이스에 Ruby on Rails 응용 프로그램을 연결합니다.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>데이터베이스 연결 구성

리포지토리에서 _config/database.yml_을 엽니다. 파일 맨 아래에서 프로덕션 변수를 다음 코드로 바꿉니다. _&lt;mysql_server_name>_ 자리 표시자의 경우 만든 MySQL 서버의 이름을 사용합니다.

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  port: 3306
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
  sslca: ssl/BaltimoreCyberTrustRoot.crt.pem
```

변경 내용을 저장합니다.

> [!NOTE]
> `sslca`가 추가되고, 샘플 리포지토리의 기존 _.pem_ 파일을 가리킵니다. 기본적으로 MySQL용 Azure 데이터베이스에는 클라이언트로부터의 SSL 연결이 적용됩니다. 이 `.pem` 인증서를 통해 Azure Database for MySQL에 대해 SSL 연결을 수행할 수 있습니다. 자세한 내용은 [MySQL용 Azure 데이터베이스에 안전하게 연결하기 위한 사용자 응용 프로그램의 SSL 연결 구성](../../mysql/howto-configure-ssl.md)을 참조하세요.
>

### <a name="test-the-application-locally"></a>로컬에서 응용 프로그램 테스트

로컬 터미널에서 다음과 같은 환경 변수를 설정합니다.

```bash
export DB_HOST=<mysql_server_name>.mysql.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<mysql_server_name>
export DB_PASSWORD=MySQLAzure2017
```

방금 구성한 프로덕션 값을 사용해서 Rails 데이터베이스 마이그레이션을 실행하고 Azure Database for MySQL(미리 보기)에서 MySQL 데이터베이스에 테이블을 만듭니다. 

```bash
rake db:migrate RAILS_ENV=production
```

프로덕션 환경에서 실행하는 경우 Rails 응용 프로그램에는 미리 컴파일된 자산이 필요합니다. 다음 명령을 사용하여 필요한 자산을 생성합니다.

```bash
rake assets:precompile
```

Rails 프로덕션 환경에서는 보안 관리를 위해 비밀도 사용하고 있습니다. 비밀 키를 생성합니다.

```bash
rails secret
```

비밀 키를 Rails 프로덕션 환경에서 사용되는 각 변수에 저장합니다. 편의상, 두 변수에 동일한 키를 사용합니다.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Rails 프로덕션 환경에서 JavaScript 및 CSS 파일을 제공하도록 합니다.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

프로덕션 환경에서 예제 응용 프로그램을 실행합니다.

```bash
rails server -e production
```

`http://localhost:3000`로 이동합니다. 오류 없이 페이지가 로드되면 Ruby on Rails 응용 프로그램이 Azure의 MySQL 데이터베이스에 연결됩니다.

해당 페이지에서 몇 가지 작업을 추가합니다.

![Ruby on Rails는 Azure Database for MySQL(미리 보기)에 성공적으로 연결됩니다.](./media/tutorial-ruby-mysql-app/azure-mysql-connect-success.png)

Rails 서버를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

### <a name="commit-your-changes"></a>변경 내용을 커밋합니다

다음 Git 명령을 실행하여 변경 내용을 커밋합니다.

```bash
git add .
git commit -m "database.yml updates"
```

앱을 배포할 준비가 되었습니다.

## <a name="deploy-to-azure"></a>Deploy to Azure

이 단계에서는 MySQL에 연결된 Rails 응용 프로그램을 Azure App Service에 배포합니다.

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service 계획 만들기

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>웹앱 만들기

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>데이터베이스 설정 구성

Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az_webapp_config_appsettings_set) 명령을 사용하여 App Service의 환경 변수를 _앱 설정_으로 설정합니다.

다음 Cloud Shell 명령에서는 `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` 및 `DB_PASSWORD` 앱 설정을 구성합니다. _&lt;appname>_ 및 _&lt;mysql_server_name>_ 자리 표시자를 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<mysql_server_name>.mysql.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<mysql_server_name>" DB_PASSWORD="MySQLAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Rails 환경 변수 구성

로컬 터미널에서 Rails 프로덕션 환경에 대한 새 비밀 키를 생성합니다.

```bash
rails secret
```

Rails 프로덕션 환경에 필요한 변수를 구성합니다.

다음 Cloud Shell 명령에서 두 개의 _&lt;output_of_rails_secret>_ 자리 표시자를 로컬 터미널에서 생성한 새 비밀 키로 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

`ASSETS_PRECOMPILE="true"`는 각 Git 배포의 자산을 미리 컴파일하도록 기본 Ruby 컨테이너에 지시합니다.

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

로컬 터미널에서 로컬 Git 리포지토리에 Azure 원격을 추가합니다.

```bash
git remote add azure <paste_copied_url_here>
```

Azure 원격 위치에 푸시하여 Ruby on Rails 응용 프로그램을 배포합니다. 배포 사용자를 만드는 작업의 일부로 이전에 제공한 암호를 묻는 메시지가 표시됩니다.

```bash
git push azure master
```

배포하는 동안 Azure App Service는 진행 상황을 Git에 전합니다.

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

### <a name="browse-to-the-azure-web-app"></a>Azure 웹앱 찾아보기

`http://<app_name>.azurewebsites.net`으로 이동한 후 목록에 몇 가지 작업을 추가합니다.

![Azure App Service에서 실행 중인 Ruby on Rails 앱](./media/tutorial-ruby-mysql-app/ruby-mysql-in-azure.png)

축하합니다! Azure App Service에서 데이터 기반 Ruby on Rails 앱을 실행하고 있습니다.

## <a name="update-model-locally-and-redeploy"></a>로컬에서 모델 업데이트 및 다시 배포

이 단계에서는 `task` 데이터 모델과 웹앱을 간단히 변경한 다음 업데이트를 Azure에 게시합니다.

작업 시나리오의 경우 작업을 완료한 것으로 표시할 수 있도록 응용 프로그램을 수정합니다.

### <a name="add-a-column"></a>열 추가

터미널에서 Git 리포지토리의 루트로 이동합니다.

`Tasks` 테이블에 `Done`이라는 부울 열을 추가하는 새 마이그레이션을 생성합니다.

```bash
rails generate migration add_Done_to_Tasks Done:boolean
```

이 명령은 _db/migrate_ 디렉터리에 새 마이그레이션 파일을 생성합니다.


터미널에서 Rails 데이터베이스 마이그레이션을 실행하여 로컬 데이터베이스를 변경합니다.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>응용 프로그램 논리 업데이트

*app/controllers/tasks_controller.rb* 파일을 엽니다. 파일 끝에 다음 줄을 추가합니다.

```rb
params.require(:task).permit(:Description)
```

새 `Done` 매개 변수를 포함하도록 이 줄을 수정합니다.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>보기 업데이트

편집 형식에 해당하는 *app/views/tasks/_form.html.erb* 파일을 엽니다.

`<%=f.error_span(:Description) %>` 줄을 찾은 후 바로 아래에 다음 코드를 삽입합니다.

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

단일 레코드 보기 페이지인 *app/views/tasks/show.html.erb* 파일을 엽니다. 

`<dd><%= @task.Description %></dd>` 줄을 찾은 후 바로 아래에 다음 코드를 삽입합니다.

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

모레코든 드의 인덱스 페이지인 *app/views/tasks/index.html.erb* 파일을 엽니다.

`<th><%= model_class.human_attribute_name(:Description) %></th>` 줄을 찾은 후 바로 아래에 다음 코드를 삽입합니다.

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

같은 파일에서 `<td><%= task.Description %></td>` 줄을 찾은 후 바로 아래에 다음 코드를 삽입합니다.

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>로컬에서 변경 내용 테스트

로컬 터미널에서 Rails 서버를 실행합니다.

```bash
rails server
```

작업 상태가 변경되는 것을 확인하려면 `http://localhost:3000`으로 이동하고 항목을 추가하거나 편집합니다.

![작업에 확인란이 추가됨](./media/tutorial-ruby-mysql-app/complete-checkbox.png)

Rails 서버를 중지하려면 터미널에서 `Ctrl + C`를 입력합니다.

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

터미널에서 프로덕션 환경에 대해 Rails 데이터베이스 마이그레이션을 실행하고, Azure 데이터베이스를 변경합니다.

```bash
rake db:migrate RAILS_ENV=production
```

Git에서 모든 변경 내용을 커밋한 다음 Azure에 코드 변경 내용을 푸시합니다.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

`git push`가 완료되면 Azure 웹앱으로 이동하여 새 기능을 테스트해 봅니다.

![Azure에 게시된 모델 및 데이터베이스 변경 내용](media/tutorial-ruby-mysql-app/complete-checkbox-published.png)

모든 작업을 추가했으면 데이터베이스에서 유지됩니다. 데이터 스키마를 업데이트하는 경우 기존 데이터는 그대로 유지됩니다.

## <a name="manage-the-azure-web-app"></a>Azure 웹앱 관리

만든 웹앱을 관리하려면 [Azure Portal](https://portal.azure.com)로 이동합니다.

왼쪽 메뉴에서 **App Services**를 클릭한 다음 Azure 웹앱의 이름을 클릭합니다.

![Azure 웹앱에 대한 포털 탐색](./media/tutorial-php-mysql-app/access-portal.png)

웹앱의 개요 페이지가 표시됩니다. 여기서 중지, 시작, 다시 시작, 찾아보기 및 삭제와 같은 기본 관리 작업을 수행할 수 있습니다.

왼쪽 메뉴에서 앱을 구성하기 위한 페이지를 제공합니다.

![Azure Portal의 App Service 페이지](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure에서 MySQL 데이터베이스 만들기
> * Ruby on Rails 앱을 MySQL에 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

다음 자습서로 이동하여 사용자 지정 DNS 이름을 웹앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Web Apps에 기존 사용자 지정 DNS 이름 매핑](../app-service-web-tutorial-custom-domain.md)
