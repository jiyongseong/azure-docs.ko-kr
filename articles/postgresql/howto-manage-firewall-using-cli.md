---
title: Azure CLI를 사용한 PostgreSQL용 Azure Database 방화벽 규칙 만들기 및 관리
description: 이 문서에서는 Azure CLI 명령줄을 사용하여 PostgreSQL용 Azure 데이터베이스 방화벽 규칙을 만들고 관리하는 방법을 설명합니다.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 05/4/2018
ms.openlocfilehash: ba5533184331b3692882b224b77ad1f38e970661
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Azure CLI를 사용한 PostgreSQL용 Azure Database 방화벽 규칙 만들기 및 관리
관리자는 서버 수준 방화벽 규칙을 사용하여 특정 IP 주소 또는 IP 주소 범위에서 PostgreSQL용 Azure Database 서버에 대한 액세스를 관리할 수 있습니다. 편리한 Azure CLI 명령을 사용하면 서버를 관리하는 방화벽 규칙을 만들고, 업데이트하고, 삭제하며, 표시할 수 있습니다. PostgreSQL용 Azure Database 방화벽 규칙에 대한 개요는 [PostgreSQL용 Azure Database 서버 방화벽 규칙](concepts-firewall-rules.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure CLI 2.0](/cli/azure/install-azure-cli) 명령줄 유틸리티를 설치하거나, 브라우저에서 Azure Cloud Shell을 사용합니다.
- [PostgreSQL용 Azure Database 서버 및 데이터베이스](quickstart-create-server-database-azure-cli.md)

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>PostgreSQL용 Azure Database에 대한 서버 방화벽 규칙 구성
[az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule) 명령은 방화벽 규칙을 구성하는 데 사용됩니다.

## <a name="list-firewall-rules"></a>방화벽 규칙 나열 
기존 서버 방화벽 규칙을 나열하려면 [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_list) 명령을 실행합니다.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
출력에 기본적으로 JSON 형식으로 방화벽 규칙(있는 경우)이 나열됩니다. 읽기 쉬운 테이블 형식으로 출력하기 위해 `--output table` 스위치를 사용할 수도 있습니다.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>방화벽 규칙 만들기
서버에 새 방화벽 규칙을 만들려면 [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_create) 명령을 실행합니다. 

다음 예제에서는 0.0.0.0을 `--start-ip-address` 범위로, 255.255.255.255를 `--end-ip-address` 범위로 지정하여 모든 IP 주소 범위에서 **mydemoserver.postgres.database.azure.com** 서버에 액세스하도록 허용합니다.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```
단일 IP 주소에 대한 액세스를 허용하려면 이 예제와 같이 `--start-ip-address` 및 `--end-ip-address`에 동일한 주소를 제공합니다.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Azure IP 주소의 응용 프로그램에서 Azure Database for PostgreSQL 서버에 연결할 수 있게 하려면 다음 예제와 같이 0.0.0.0 IP 주소를 시작 IP와 끝 IP로 제공합니다.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> 이 옵션은 다른 고객 구독에서의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.
> 

성공하면 명령 출력은 사용자가 만든 방화벽 규칙의 세부 정보를 기본적으로 JSON 형식으로 나열합니다. 오류가 있는 경우 출력에는 오류 메시지가 대신 표시됩니다.

## <a name="update-firewall-rule"></a>방화벽 규칙 업데이트 
[az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_update) 명령을 사용하여 서버에서 기존 방화벽 규칙을 업데이트합니다. 기존 방화벽 규칙의 이름과 업데이트할 시작 IP 및 끝 IP 특성을 입력합니다.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.255
```
성공하면 명령 출력은 업데이트한 방화벽 규칙의 세부 정보를 기본적으로 JSON 형식으로 나열합니다. 오류가 있는 경우 출력에는 오류 메시지가 대신 표시됩니다.
> [!NOTE]
> 방화벽 규칙이 존재하지 않는 경우 업데이트 명령으로 생성됩니다.

## <a name="show-firewall-rule-details"></a>방화벽 규칙 세부 정보 표시
[az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_show) 명령을 실행하여 기존 서버 수준 방화벽 규칙 세부 정보를 표시할 수도 있습니다.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
성공하면 명령 출력은 지정한 방화벽 규칙의 세부 정보를 기본적으로 JSON 형식으로 나열합니다. 오류가 있는 경우 출력에는 오류 메시지가 대신 표시됩니다.

## <a name="delete-firewall-rule"></a>방화벽 규칙 삭제
IP 범위의 서버에 대한 액세스를 해지하려면 [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule#az_postgres_server_firewall_rule_delete) 명령을 실행하여 기존 방화벽 규칙을 삭제합니다. 기존 방화벽 규칙의 이름을 제공합니다.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
성공하면 출력은 없습니다. 실패하면 오류 메시지 텍스트가 반환됩니다.

## <a name="next-steps"></a>다음 단계
- 마찬가지로 웹 브라우저를 통해 [Azure Portal을 사용한 PostgreSQL용 Azure Database 방화벽 규칙 만들기 및 관리](howto-manage-firewall-using-portal.md)를 확인할 수 있습니다.
- [PostgreSQL용 Azure Database 서버 방화벽 규칙](concepts-firewall-rules.md)에 대해 자세히 알아보세요.
- PostgreSQL용 Azure Database 서버 연결에 대한 도움말은 [PostgreSQL용 Azure Database에 대한 연결 라이브러리](concepts-connection-libraries.md)를 참조하세요.
