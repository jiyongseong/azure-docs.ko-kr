---
title: CLI 예제 모니터링 크기 조정 단일 SQL Database | Microsoft Docs
description: 단일 Azure SQL Database를 모니터링하고 크기를 조정하는 Azure CLI 예제 스크립트
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: craigg
editor: carlrab
tags: azure-service-management
ms.assetid: ''
ms.service: sql-database
ms.custom: monitor & tune, mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 00464de212b7a2faa62eeac15bc4db1fd0f84b4f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364440"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>CLI를 사용하여 단일 SQL Database 모니터링 및 크기 조정

이 Azure CLI 스크립트 예제는 데이터베이스에 대한 크기 정보를 쿼리한 후에 단일 Azure SQL Database를 다양한 성능 수준으로 확장합니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> [az sql db op list](/cli/azure/sql/db/op?#az_sql_db_op_list)를 사용하여 데이터베이스에서 수행된 작업 목록을 가져오고 [az sql db op cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel)을 사용하여 데이터베이스에서 업데이트 작업을 취소합니다.

## <a name="clean-up-deployment"></a>배포 정리

스크립트 샘플을 실행한 후에 다음 명령을 사용하여 리소스 그룹 및 관련된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az_sql_server_create) | 데이터베이스를 호스트하는 논리 서버를 만듭니다. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_show_usage) | 데이터베이스의 크기 사용량 정보가 표시됩니다. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az_sql_db_update) | 데이터베이스 속성(예: 서비스 계층 또는 성능 수준)을 업데이트하거나 데이터베이스 정보를 탄력적 풀로/에서 또는 탄력적 풀 간에 이동합니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 SQL Database CLI 스크립트 샘플은 [Azure SQL Database 설명서](../sql-database-cli-samples.md)에서 찾을 수 있습니다.
