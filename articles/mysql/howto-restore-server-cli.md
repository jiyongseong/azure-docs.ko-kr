---
title: Azure Database for MySQL에서 서버를 백업 및 복원하는 방법
description: Azure CLI를 사용하여 Azure Database for MySQL에서 서버를 백업 및 복원하는 방법을 알아봅니다.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: article
ms.date: 04/01/2018
ms.openlocfilehash: bd4ebbec4506824f00d09a09369ebbeaf9458c19
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure Database for MySQL에서 서버를 백업 및 복원하는 방법

## <a name="backup-happens-automatically"></a>자동으로 수행되는 백업
Azure Database for MySQL 서버는 정기적으로 백업되어 복원 기능을 사용하도록 설정할 수 있습니다. 이 기능을 사용하면 서버 및 모든 데이터베이스를 이전 특정 시점으로 새 서버에 복원할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 완료하려면 다음이 필요합니다.
- [Azure Database for MySQL 서버 및 데이터베이스](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> 이 방법 가이드에서는 Azure CLI 버전 2.0 이상을 사용해야 합니다. 버전을 확인하려면 Azure CLI 명령 프롬프트에서 `az --version`을 입력합니다. 설치하거나 업그레이드하려면 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="add-the-extension"></a>확장 추가
다음 명령을 사용하여 업데이트된 Azure Database for MySQL 관리 확장을 추가합니다.
```azurecli-interactive
az extension add --name rdbms
``` 

올바른 확장 버전이 설치되어 있는지 확인합니다. 
```azurecli-interactive
az extension list
```

반환된 JSON에는 다음이 포함되어야 합니다. 
```json
{
    "extensionType": "whl",
    "name": "rdbms",
    "version": "0.0.5"
}
```

0.0.5 버전이 반환되지 않으면 다음을 실행하여 확장을 업데이트합니다. 
```azurecli-interactive
az extension update --name rdbms
```


## <a name="set-backup-configuration"></a>백업 구성 설정

서버를 만들 때 로컬 중복 백업 또는 지역 중복 백업을 위한 서버 구성 중에서 선택할 수 있습니다. 

> [!NOTE]
> 서버가 만들어지면 지리적으로 중복되거나 로컬로 중복된 중복 형식은 전환할 수 없습니다.
>

`az mysql server create` 명령을 통해 서버를 만드는 동안 `--geo-redundant-backup` 매개 변수는 백업 중복성 옵션을 결정합니다. `Enabled`인 경우 지역 중복 백업이 수행됩니다. 또는 `Disabled`인 경우 로컬 중복 백업이 수행됩니다. 

백업 보존 기간은 `--backup-retention` 매개 변수에 의해 설정됩니다. 

만드는 중에 이러한 값을 설정하는 방법에 대한 자세한 내용은 [Azure Database for MySQL 서버 CLI 빠른 시작](quickstart-create-mysql-server-database-using-azure-cli.md)을 참조하세요.

서버의 백업 보존 기간은 다음과 같이 변경할 수 있습니다.

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

앞의 예제는 mydemoserver의 백업 보존 기간을 10일로 변경합니다.

백업 보존 기간은 사용 가능한 백업을 기반으로 하기 때문에 특정 시점 복원을 검색할 수 있는 시간을 제어합니다. 특정 시점 복원은 다음 섹션에서 자세히 설명합니다.

## <a name="server-point-in-time-restore"></a>서버 지정 시간 복원
이전의 특정 시점으로 서버를 복원할 수 있습니다. 복원된 데이터는 새 서버에 복사되고 기존 서버는 그대로 유지됩니다. 예를 들어 테이블이 오늘 정오에 실수로 삭제된 경우 정오 바로 전 시간으로 복원할 수 있습니다. 그런 다음 서버의 복원된 복사본에서 누락된 테이블 및 데이터를 검색할 수 있습니다. 

서버를 복원하려면 Azure CLI [az mysql server restore](/cli/azure/mysql/server#az_mysql_server_restore) 명령을 사용합니다.

### <a name="run-the-restore-command"></a>복원 명령 실행

서버를 복원하려면 Azure CLI 명령 프롬프트에서 다음 명령을 입력합니다.

```azurecli-interactive
az mysql server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

`az mysql server restore` 명령에는 다음과 같은 매개 변수가 필요합니다.
| 설정 | 제안 값 | 설명  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  원본 서버가 있는 리소스 그룹입니다.  |
| 이름 | mydemoserver-restored | 복원 명령에 의해 만들어진 새 서버의 이름입니다. |
| restore-point-in-time | 2018-03-13T13:59:00Z | 복원할 특정 시점을 선택합니다. 이 날짜 및 시간은 원본 서버의 백업 보존 기간 내에 있어야 합니다. ISO8601 날자 및 시간 형식을 사용합니다. 예를 들어 `2018-03-13T05:59:00-08:00`과 같이 현지 표준 시간대를 사용할 수 있습니다. UTC Zulu 형식을 사용할 수도 있습니다(예: `2018-03-13T13:59:00Z`). |
| source-server | mydemoserver | 복원을 수행하려는 원본 서버의 이름 또는 ID입니다. |

서버를 이전 특정 시점으로 복원하는 경우 새 서버가 만들어집니다. 지정된 특정 시점의 원본 서버 및 해당 데이터베이스가 새 서버에 복사됩니다.

복원된 서버에 대한 위치 및 가격 책정 계층 값은 원본 서버와 같게 유지됩니다. 

복원 프로세스가 완료된 후 새 서버를 찾아 데이터가 예상대로 복원되었는지 확인합니다.

## <a name="geo-restore"></a>지역 복원
서버를 지리적으로 중복된 백업으로 구성한 경우 기존 서버의 백업에서 새 서버를 만들 수 있습니다. 이 새 서버는 Azure Database for MySQL을 사용할 수 있는 모든 지역에서 만들 수 있습니다.  

지역 중복 백업을 사용하여 서버를 만들려면 Azure CLI `az mysql server georestore` 명령을 사용합니다.

> [!NOTE]
> 서버가 처음 생성될 때는 지역 복원에 즉시 사용 가능하지 않을 수 있습니다. 필요한 메타데이터를 채우는 데 몇 시간 정도 걸릴 수 있습니다.
>

서버를 지역 복원하려면 Azure CLI 명령 프롬프트에서 다음 명령을 입력합니다.

```azurecli-interactive
az mysql server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
이 명령은 *myresourcegroup*에 속하는 미국 동부에 *mydemoserver-georestored*라는 새 서버를 만듭니다. 이 서버는 vCore가 8개인 범용 4세대 서버입니다. 서버가 *mydemoserver*의 지역 중복 백업에서 생성되며 이는 리소스 그룹 *myresourcegroup*에도 있습니다.

기존 서버에서 다른 리소스 그룹에 새 서버를 만들려는 경우 `--source-server` 매개 변수에서 다음 예제에서와 같이 서버 이름을 한정합니다.

```azurecli-interactive
az mysql server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforMySQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

`az mysql server georestore` 명령에는 다음과 같은 매개 변수가 필요합니다.
| 설정 | 제안 값 | 설명  |
| --- | --- | --- |
|resource-group| myresourcegroup | 새 서버가 속하게 되는 리소스 그룹의 이름입니다.|
|이름 | mydemoserver-georestored | 새 서버의 이름입니다. |
|source-server | mydemoserver | 해당 지역 중복 백업이 사용되는 기존 서버의 이름입니다. |
|location | eastus | 새 서버의 위치입니다. |
|sku-name| GP_Gen4_8 | 이 매개 변수는 가격 책정 계층, 계산 생성 및 새 서버의 vCore 수를 설정합니다. GP_Gen4_8은 vCore가 8개인 범용 4세대 서버로 매핑합니다.|


>[!Important]
>지역 복원으로 새 서버를 만들 때 원본 서버와 동일한 저장소 크기 및 가격 책정 계층을 상속합니다. 만드는 동안 이러한 값을 변경할 수 없습니다. 새 서버를 만든 후에 저장소 크기를 확장할 수 있습니다.

복원 프로세스가 완료된 후 새 서버를 찾아 데이터가 예상대로 복원되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
- 서비스의 [백업](concepts-backup.md)을 자세히 알아봅니다.
- [비즈니스 연속성](concepts-business-continuity.md) 옵션을 자세히 알아봅니다.
