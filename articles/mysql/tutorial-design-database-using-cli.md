---
title: '자습서: Azure CLI를 사용하여 Azure Database for MySQL 디자인'
description: 이 자습서에서는 명령줄에서 Azure CLI 2.0을 사용하여 MySQL용 Azure Database 서버 및 데이터베이스를 만들고 관리하는 방법을 설명합니다.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: tutorial
ms.date: 04/01/2018
ms.custom: mvc
ms.openlocfilehash: 3a06374119851560d517704b817fb9bf18728059
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="tutorial-design-an-azure-database-for-mysql-using-azure-cli"></a>자습서: Azure CLI를 사용하여 Azure Database for MySQL 디자인

Azure Database for MySQL은 MySQL Community Edition 데이터베이스 엔진을 기반으로 한 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다 이 자습서에서는 Azure CLI(명령줄 인터페이스) 및 기타 유틸리티를 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Database for MySQL 만들기
> * 서버 방화벽 구성
> * [mysql 명령줄 도구](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)를 사용하여 데이터베이스 만들기
> * 샘플 데이터 로드
> * 쿼리 데이터
> * 데이터 업데이트
> * 데이터 복원

브라우저에서 Azure Cloud Shell을 사용하거나 컴퓨터에 [Azure CLI 2.0을 설치]( /cli/azure/install-azure-cli)하여 이 자습서의 코드 블록을 실행할 수 있습니다.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

구독이 여러 개인 경우 리소스가 있거나 요금이 청구되는 적절한 구독을 선택합니다. [az account set](/cli/azure/account#az_account_set) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
[az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) 명령을 사용하여 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제는 `westus` 위치에 `myresourcegroup`이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버 만들기
az mysql server create 명령을 사용하여 Azure Database for MySQL 서버를 만듭니다. 서버는 여러 데이터베이스를 관리할 수 있습니다. 일반적으로 각 프로젝트 또는 각 사용자에 대해 별도의 데이터베이스가 사용됩니다.

다음 예제에서는 `westus`에 있는 MySQL용 Azure Database 서버를 `mydemoserver`라는 이름으로 `myresourcegroup` 리소스 그룹에 만듭니다. 서버에는 `myadmin`이라는 관리자 로그인이 있습니다. 이 서버는 vCore가 2개인 범용 4세대 서버입니다. `<server_admin_password>`를 자신의 고유한 값으로 직접 바꿉니다.

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```
> [!IMPORTANT]
> 여기에 지정한 서버 관리자 로그인 및 암호는 이 빠른 시작의 뒷부분에 나오는 서버 및 데이터베이스에 로그인하는 데 필요합니다. 나중에 사용하기 위해 이 정보를 기억하거나 기록합니다.


## <a name="configure-firewall-rule"></a>방화벽 규칙 구성
az mysql server firewall-rule create 명령을 사용하여 Azure Database for MySQL 서버 수준 방화벽 규칙을 만듭니다. 서버 수준 방화벽 규칙을 사용하면 **mysql** 명령줄 도구 또는 MySQL Workbench와 같은 외부 응용 프로그램에서 Azure MySQL 서비스 방화벽을 통해 서버에 연결할 수 있습니다. 

다음 예제에서는 미리 정의된 주소 범위에 대한 방화벽 규칙을 만듭니다. 이 예제에서는 가능한 IP 주소 범위 전체를 보여 줍니다.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowAllIPs --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

## <a name="get-the-connection-information"></a>연결 정보 가져오기

서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

결과는 JSON 형식입니다. **fullyQualifiedDomainName** 및 **administratorLogin**을 기록해 둡니다.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
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
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>mysql을 사용하여 서버에 연결
[mysql 명령줄 도구](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)를 사용하여 Azure Database for MySQL 서버에 연결합니다. 이 예제에서 명령은 다음과 같습니다.
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>빈 데이터베이스 만들기
서버에 연결되면 빈 데이터베이스를 만듭니다.
```sql
mysql> CREATE DATABASE mysampledb;
```

프롬프트에서 다음 명령을 실행하여 새로 만든 이 데이터베이스에 대한 연결로 전환합니다.
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>데이터베이스에서 테이블 만들기
이제 Azure Database for MySQL 데이터베이스에 연결하는 방법을 알았으므로 몇 가지 기본 작업을 수행할 수 있습니다.

먼저 테이블을 만들고 일부 데이터와 함께 로드합니다. 인벤토리 정보를 저장하는 테이블을 만들어 보겠습니다.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>테이블에 데이터 로드
이제 테이블을 만들었으므로 이 테이블에 일부 데이터를 삽입합니다. 열린 명령 프롬프트 창에서 다음 쿼리를 실행하여 데이터 행을 일부 삽입합니다.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

이제 앞에서 만든 테이블에 두 개의 샘플 데이터 행이 있습니다.

## <a name="query-and-update-the-data-in-the-tables"></a>테이블의 데이터 쿼리 및 업데이트
다음 쿼리를 실행하여 데이터베이스 테이블에서 정보를 검색합니다.
```sql
SELECT * FROM inventory;
```

또한 테이블의 데이터를 업데이트할 수도 있습니다
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

이에 따라 데이터를 검색할 때 해당 행이 업데이트됩니다.
```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>이전 시점으로 데이터베이스 복원
실수로 이 테이블을 삭제했다고 가정해 보겠습니다. 이런 경우는 쉽게 복구할 수 없는 경우입니다. Azure Database for MySQL을 사용하면 마지막 시점에서 최대 35일 전의 특정 시점으로 되돌아가 이 시점의 새 서버로 복원할 수 있습니다. 이 새 서버를 사용하여 삭제된 데이터를 복구할 수 있습니다. 다음 단계에서는 테이블이 추가되기 이전 시점으로 샘플 서버를 복원합니다.

복원에는 다음 정보가 필요합니다.

- 복원 지점: 데이터베이스를 변경하기 전의 특정 시점을 선택합니다. 원본 데이터베이스의 가장 오래된 백업 값보다 크거나 같아야 합니다.
- 대상 서버: 복원해 두려는 새 서버의 이름을 제공합니다.
- 원본 서버: 복원해 오려는 서버의 이름을 제공합니다.
- 위치: 지역은 선택할 수 없으며, 기본적으로 원본 서버와 동일합니다.

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time "2017-05-4 03:10" --source-server-name mydemoserver
```

`az mysql server restore` 명령에는 다음 매개 변수가 필요합니다.
| 설정 | 제안 값 | 설명  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  원본 서버가 존재하는 리소스 그룹입니다.  |
| 이름 | mydemoserver-restored | 복원 명령에 의해 만들어진 새 서버의 이름입니다. |
| restore-point-in-time | 2017-04-13T13:59:00Z | 복원하려는 지정 시간을 선택합니다. 이 날짜 및 시간은 원본 서버의 백업 보존 기간 내에 있어야 합니다. ISO8601 날자 및 시간 형식을 사용합니다. 예를 들어 `2017-04-13T05:59:00-08:00`과 같은 고유한 현지 표준 시간대 또는 UTC Zulu 형식 `2017-04-13T13:59:00Z`를 사용할 수도 있습니다. |
| source-server | mydemoserver | 복원을 수행하려는 원본 서버의 이름 또는 ID입니다. |

서버를 지정 시간으로 복원하면 새 서버가 만들어지고 사용자가 지정한 시점의 원본 서버로 복사됩니다. 복원된 서버에 대한 위치 및 가격 책정 계층 값은 원본 서버와 동일합니다.

명령은 동기화되고 서버가 복원된 후에 반환됩니다. 복원이 완료되면 생성된 새 서버를 찾습니다. 데이터가 예상대로 복원되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음에 대해 알아보았습니다.
> [!div class="checklist"]
> * Azure Database for MySQL 서버 만들기
> * 서버 방화벽 구성
> * [mysql 명령줄 도구](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)를 사용하여 데이터베이스 만들기
> * 샘플 데이터 로드
> * 쿼리 데이터
> * 데이터 업데이트
> * 데이터 복원

> [!div class="nextstepaction"]
> [Azure Database for MySQL - Azure CLI 샘플](./sample-scripts-azure-cli.md)
