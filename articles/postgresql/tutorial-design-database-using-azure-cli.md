---
title: '자습서: Azure CLI를 사용하여 Azure Database for PostgreSQL 디자인'
description: 이 자습서에서는 Azure CLI를 사용하여 첫 번째 Azure Database for PostgreSQL 서버를 만들고, 구성하고, 쿼리하는 방법을 보여줍니다.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 04/01/2018
ms.openlocfilehash: acba480631ba69a81da3029aadfb9cb51797549a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-design-an-azure-database-for-postgresql-using-azure-cli"></a>자습서: Azure CLI를 사용하여 Azure Database for PostgreSQL 디자인 
이 자습서에서는 Azure CLI(명령줄 인터페이스) 및 기타 유틸리티를 사용하여 다음을 수행하는 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * PostgreSQL용 Azure Database 서버 만들기
> * 서버 방화벽 구성
> * [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) 유틸리티를 사용하여 데이터베이스 만들기
> * 샘플 데이터 로드
> * 쿼리 데이터
> * 데이터 업데이트
> * 데이터 복원

브라우저에서 Azure Cloud Shell을 사용하거나 컴퓨터에 [Azure CLI 2.0을 설치]( /cli/azure/install-azure-cli)하여 이 자습서의 명령을 실행할 수 있습니다.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

구독이 여러 개인 경우 리소스가 있거나 요금이 청구되는 적절한 구독을 선택합니다. [az account set](/cli/azure/account#az_account_set) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 다음 예제는 `westus` 위치에 `myresourcegroup`이라는 리소스 그룹을 만듭니다.
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-postgresql-server"></a>PostgreSQL용 Azure Database 서버 만들기
[az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) 명령을 사용하여 [PostgreSQL용 Azure Database 서버](overview.md)를 만듭니다. 서버는 그룹으로 관리되는 데이터베이스 그룹을 포함합니다. 

다음 예제에서는 `myadmin` 서버 관리자 로그인을 사용하여 `myresourcegroup` 리소스 그룹에 `mydemoserver`이라는 서버를 만듭니다. 서버 이름은 DNS 이름에 매핑되므로 Azure에서 전역적으로 고유해야 합니다. `<server_admin_password>`를 자신의 고유한 값으로 직접 바꿉니다. 이 서버는 vCore가 2개인 범용 4세대 서버입니다.
```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 9.6
```

> [!IMPORTANT]
> 여기에 지정한 서버 관리자 로그인 및 암호는 이 빠른 시작의 뒷부분에 나오는 서버 및 데이터베이스에 로그인하는 데 필요합니다. 나중에 사용하기 위해 이 정보를 기억하거나 기록합니다.

기본적으로 **postgres** 데이터베이스가 서버 아래에 만들어집니다. [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) 데이터베이스는 사용자, 유틸리티 및 타사 응용 프로그램에서 사용하는 기본 데이터베이스입니다. 


## <a name="configure-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 구성

[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) 명령을 사용하여 Azure PostgreSQL 서버 수준 방화벽 규칙을 만듭니다. 서버 수준 방화벽 규칙을 사용하면 [psql](https://www.postgresql.org/docs/9.2/static/app-psql.html) 또는 [PgAdmin](https://www.pgadmin.org/)과 같은 외부 응용 프로그램에서 Azure PostgreSQL 서비스 방화벽을 통해 서버에 연결할 수 있습니다. 

IP 범위를 적용하는 방화벽 규칙을 설정하여 네트워크에서 연결할 수 있습니다. 다음 예제에서는 [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create)를 사용하여 모든 IP 주소에서 연결할 수 있는 방화벽 규칙 `AllowAllIps`를 만듭니다. 모든 IP 주소를 열려면 시작 IP 주소로 0.0.0.0을, 끝나는 IP 주소로 255.255.255.255를 사용합니다.

Azure PostgreSQL 서버에 대한 액세스를 네트워크로만 제한하려면 회사 네트워크 IP 주소 범위만 처리하도록 방화벽 규칙을 설정할 수 있습니다.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!NOTE]
> Azure PostgreSQL 서버는 5432 포트를 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 5432 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. IT 부서에서 포트 5432를 열어 Azure SQL Database 서버에 연결할 수 있도록 합니다.
>

## <a name="get-the-connection-information"></a>연결 정보 가져오기

서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다.
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

결과는 JSON 형식입니다. **administratorLogin** 및 **fullyQualifiedDomainName**을 기록해 둡니다.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"

}
```

## <a name="connect-to-azure-database-for-postgresql-database-using-psql"></a>psql을 사용하여 Azure Database for PostgreSQL 데이터베이스에 연결
클라이언트 컴퓨터에 PostgreSQL이 설치되어 있는 경우 [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html) 로컬 인스턴스 또는 Azure 클라우드 콘솔을 사용하여 Azure PostgreSQL 서버에 연결할 수 있습니다. 이제 psql 명령줄 유틸리티를 사용하여 PostgreSQL용 Azure Database 서버에 연결해 보겠습니다.

1. 다음 psql 명령을 실행하여 Azure Database for PostgreSQL 데이터베이스에 연결합니다.
```azurecli-interactive
psql --host=<servername> --port=<port> --username=<user@servername> --dbname=<dbname>
```

  예를 들어, 다음 명령은 액세스 자격 증명을 사용하여 **mydemoserver.postgres.database.azure.com** PostgreSQL 서버의 **postgres**라는 기본 데이터베이스에 연결합니다. 암호를 묻는 메시지가 표시되면 선택한 `<server_admin_password>`를 입력합니다.
  
  ```azurecli-interactive
psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
```

2.  서버에 연결되면 프롬프트에서 빈 데이터베이스를 만듭니다.
```sql
CREATE DATABASE mypgsqldb;
```

3.  프롬프트에서 다음 명령을 실행하여 새로 만든 **mypgsqldb** 데이터베이스에 대한 연결로 전환합니다.
```sql
\c mypgsqldb
```

## <a name="create-tables-in-the-database"></a>데이터베이스에서 테이블 만들기
이제 Azure Database for PostgreSQL에 연결하는 방법을 알았으므로 몇 가지 기본 작업을 완료할 수 있습니다.

먼저 테이블을 만들고 일부 데이터와 함께 로드합니다. 예를 들어 인벤토리 정보를 추적하는 테이블을 만듭니다.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

다음과 같이 입력하면 테이블 목록에서 새로 만든 테이블을 볼 수 있습니다.
```sql
\dt
```

## <a name="load-data-into-the-table"></a>테이블에 데이터 로드
이제 테이블을 만들었으니 여기에 일부 데이터를 삽입합니다. 열린 명령 프롬프트 창에서 다음 쿼리를 실행하여 데이터 행을 삽입합니다.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

앞에서 만든 테이블에 두 개의 샘플 데이터 행이 추가되었습니다.

## <a name="query-and-update-the-data-in-the-tables"></a>테이블의 데이터 쿼리 및 업데이트
다음 쿼리를 실행하여 인벤토리 테이블에서 정보를 검색합니다. 
```sql
SELECT * FROM inventory;
```

또한 인벤토리 테이블의 데이터를 업데이트할 수도 있습니다.
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

데이터를 검색할 때 업데이트된 값을 볼 수 있습니다.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>이전 시점으로 데이터베이스 복원
실수로 테이블을 삭제한 경우를 가정해 보겠습니다. 이런 경우는 쉽게 복구할 수 없는 경우입니다. Azure Database for PostgreSQL을 사용하면, 서버에 백업이 있는 특정 시점(구성한 백업 보존 기간에 따라 결정됨)으로 돌아가서 새 서버를 이 시점으로 복원할 수 있습니다. 이 새 서버를 사용하여 삭제된 데이터를 복구할 수 있습니다. 

다음 명령은 샘플 서버를 테이블이 추가되기 전 시점으로 복원합니다.
```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2017-04-13T13:59:00Z --source-server mydemoserver
```

`az postgres server restore` 명령에는 다음 매개 변수가 필요합니다.
| 설정 | 제안 값 | 설명  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  원본 서버가 존재하는 리소스 그룹입니다.  |
| 이름 | mydemoserver-restored | 복원 명령에 의해 만들어진 새 서버의 이름입니다. |
| restore-point-in-time | 2017-04-13T13:59:00Z | 복원하려는 지정 시간을 선택합니다. 이 날짜 및 시간은 원본 서버의 백업 보존 기간 내에 있어야 합니다. ISO8601 날자 및 시간 형식을 사용합니다. 예를 들어 `2017-04-13T05:59:00-08:00`과 같은 고유한 현지 표준 시간대 또는 UTC Zulu 형식 `2017-04-13T13:59:00Z`를 사용할 수도 있습니다. |
| source-server | mydemoserver | 복원을 수행하려는 원본 서버의 이름 또는 ID입니다. |

서버를 지정 시간으로 복원하면 새 서버가 만들어지고 사용자가 지정한 시점의 원본 서버로 복사됩니다. 복원된 서버에 대한 위치 및 가격 책정 계층 값은 원본 서버와 동일합니다.

명령은 동기화되고 서버가 복원된 후에 반환됩니다. 복원이 완료되면 생성된 새 서버를 찾습니다. 데이터가 예상대로 복원되었는지 확인합니다.


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure CLI(명령줄 인터페이스) 및 기타 유틸리티를 사용하여 다음을 수행하는 방법에 대해 알아보았습니다.
> [!div class="checklist"]
> * PostgreSQL용 Azure Database 서버 만들기
> * 서버 방화벽 구성
> * [**psql**](https://www.postgresql.org/docs/9.6/static/app-psql.html) 유틸리티를 사용하여 데이터베이스 만들기
> * 샘플 데이터 로드
> * 쿼리 데이터
> * 데이터 업데이트
> * 데이터 복원

다음에는 Azure Portal을 사용하여 유사한 작업을 수행하는 방법에 대해 알아보고, [Azure Portal을 사용하여 첫 번째 Azure Database for PostgreSQL 디자인](tutorial-design-database-using-azure-portal.md) 자습서를 검토합니다.
