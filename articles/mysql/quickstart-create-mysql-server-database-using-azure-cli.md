---
title: '빠른 시작: Azure Database for MySQL 서버 만들기 - Azure CLI'
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Azure 리소스 그룹에서 MySQL용 Azure Database 서버를 만드는 방법을 살펴봅니다.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 04/01/2018
ms.custom: mvc
ms.openlocfilehash: afab8eb981f59a4ab2ba5528e0518e370d8e05fc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI를 사용한 MySQL용 Azure 데이터베이스 서버 만들기 
이 빠른 시작에서는 약 5분 안에 Azure CLI를 사용하여 Azure 리소스 그룹에서 MySQL용 Azure Database 서버를 만드는 방법을 살펴봅니다. 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

구독이 여러 개인 경우 리소스가 있거나 요금이 청구되는 적절한 구독을 선택합니다. [az account set](/cli/azure/account#az_account_set) 명령을 사용하여 계정에 속한 특정 구독 ID를 선택합니다.
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 [Azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다.

다음 예제는 `westus` 위치에 `myresourcegroup`이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mysql-server"></a>Azure Database for MySQL 서버 만들기
**[az mysql server create](/cli/azure/mysql/server#az_mysql_server_create)** 명령을 사용하여 Azure Database for MySQL 서버를 만듭니다. 서버는 여러 데이터베이스를 관리할 수 있습니다. 일반적으로 각 프로젝트 또는 각 사용자에 대해 별도의 데이터베이스가 사용됩니다.

다음 예제에서는 미국 서부에 `myadmin` 서버 관리자 로그인을 사용하여 `myresourcegroup` 리소스 그룹에 `mydemoserver`라는 서버를 만듭니다. 이 서버는 **vCore**가 2개인 **4세대** **범용** 서버입니다. 서버 이름은 DNS 이름에 매핑되므로 Azure에서 전역적으로 고유해야 합니다. `<server_admin_password>`를 자신의 고유한 값으로 직접 바꿉니다.
```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen4_2 --version 5.7
```

## <a name="configure-firewall-rule"></a>방화벽 규칙 구성
**[az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az_mysql_server_firewall_rule_create)** 명령을 사용하여 MySQL용 Azure Database 서버 수준 방화벽 규칙을 만듭니다. 서버 수준 방화벽 규칙을 사용하면 **mysql.exe** 명령줄 도구 또는 MySQL Workbench와 같은 외부 응용 프로그램에서 Azure MySQL 서비스 방화벽을 통해 서버에 연결할 수 있습니다. 

다음 예제에서는 미리 정의된 주소 범위(이 예제에서는 전체 가능한 IP 주소 범위)에 대해 방화벽 규칙을 만듭니다.

```azurecli-interactive
az mysql server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowYourIP --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
모든 IP 주소 허용은 안전하지 않습니다. 이 예제는 편의상 제공되지만 실제 시나리오에서는 응용 프로그램 및 사용자에 대해 추가할 정확한 IP 주소 범위를 파악해야 합니다. 

> [!NOTE]
> Azure Database for MySQL에 대한 연결은 포트 3306을 통해 통신합니다. 회사 네트워크 내에서 연결하려고 하면 3306 포트를 통한 아웃바운드 트래픽이 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 3306 포트를 열지 않으면 서버에 연결할 수 없습니다.
> 


## <a name="configure-ssl-settings"></a>SSL 설정 구성
기본적으로 서버와 클라이언트 응용 프로그램 간에 SSL 연결이 적용됩니다. 이 기본 인터넷을 통해 데이터 스트림을 암호화하여 "이동 중"인 데이터를 보호할 수 있습니다. 이 빠른 시작을 간단하게 하기 위해 서버에 대해 SSL 연결을 사용하지 않도록 설정합니다. SSL 비활성화는 프로덕션 서버에는 권장되지 않습니다. 자세한 내용은 [MySQL용 Azure 데이터베이스에 안전하게 연결하기 위한 사용자 응용 프로그램의 SSL 연결 구성](./howto-configure-ssl.md)을 참조하세요.

다음 예에서는 MySQL 서버에 SSL을 적용하지 않습니다.
 
 ```azurecli-interactive
 az mysql server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
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
  "earliestRestoreDate": null,
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

## <a name="connect-to-the-server-using-the-mysqlexe-command-line-tool"></a>mysql.exe 명령줄 도구를 사용하여 서버에 연결
**mysql.exe** 명령줄 도구를 사용하여 서버에 연결. [여기](https://dev.mysql.com/downloads/)에서 MySQL을 다운로드하여 컴퓨터에 설치하면 됩니다. 대신 예제 코드에서 **시도** 단추를 클릭하거나 Azure Portal의 오른쪽 상단 도구 모음에서 `>_` 단추를 클릭하고 **Azure Cloud Shell**을 시작할 수도 있습니다.

다음 명령을 입력합니다. 

1. **mysql** 명령줄 도구를 사용하여 서버에 연결:
```azurecli-interactive
 mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

2. 서버 상태 보기:
```sql
 mysql> status
```
모든 작업이 제대로 진행되었다면 명령줄 도구에서 다음 텍스트가 출력될 것입니다.

```dos
C:\Users\>mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.26.0 MySQL Community Server (GPL)

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Win64 (x86_64)

Connection id:          65512
Current database:
Current user:           myadmin@116.230.243.143
SSL:                    Not in use
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             mydemoserver.mysql.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    gbk
Conn.  characterset:    gbk
TCP port:               3306
Uptime:                 2 days 9 hours 47 min 20 sec

Threads: 4  Questions: 34833  Slow queries: 2  Opens: 84  Flush tables: 4  Open tables: 1  Queries per second avg: 0.167
--------------

mysql>
```

> [!TIP]
> 다른 명령은 [MySQL 5.7 참조 설명서 - 4.5.1장](https://dev.mysql.com/doc/refman/5.7/en/mysql.html)을 참조하세요.

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>MySQL Workbench GUI 도구로 서버에 연결
1.  클라이언트 컴퓨터에서 MySQL Workbench 응용 프로그램을 실행합니다. MySQL Workbench는 [여기](https://dev.mysql.com/downloads/workbench/)에서 다운로드할 수 있습니다.

2.  **새 연결 설정** 대화 상자의 **매개 변수** 탭에 다음 정보를 입력합니다.

   ![새 연결 설정](./media/quickstart-create-mysql-server-database-using-azure-cli/setup-new-connection.png)

| **설정** | **제안 값** | **설명** |
|---|---|---|
|   연결 이름 | 내 연결 | 이 연결에 대한 레이블 지정(아무 이름이나 가능) |
| 연결 방법 | 표준(TCP/IP) 선택 | TCP/IP 프로토콜을 사용하여 MySQL에 대한 Azure Database에 연결> |
| 호스트 이름 | mydemoserver.mysql.database.azure.com | 앞에서 기록한 서버 이름입니다. |
| 포트 | 3306 | MySQL의 기본 포트가 사용됩니다. |
| 사용자 이름 | myadmin@mydemoserver | 앞에서 기록한 서버 관리자 로그인 |
| 암호 | **** | 이전에 구성한 관리자 계정 암호를 사용합니다. |

**연결 테스트**를 클릭하여 모든 매개 변수가 올바르게 구성되었는지 테스트합니다.
이제 연결을 클릭하여 서버에 연결할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리
다른 빠른 시작/자습서에서 이러한 리소스가 필요하지 않으면 다음 명령을 수행하여 삭제할 수 있습니다. 

```azurecli-interactive
az group delete --name myresourcegroup
```

새로 만든 서버 하나만 삭제하려면 **[az mysql server delete](/cli/azure/mysql/server#az_mysql_server_delete)** 명령을 실행할 수 있습니다.
```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure CLI로 MySQL 데이터베이스 디자인](./tutorial-design-database-using-cli.md)
