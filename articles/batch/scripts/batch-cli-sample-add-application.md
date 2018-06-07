---
title: "Azure CLI 스크립트 예제 - Batch로 응용 프로그램 추가 | Microsoft Docs"
description: "Azure CLI 스크립트 예제 - Batch로 응용 프로그램 추가"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: 80b9cb749be942f72459180182e7d74a45f6a943
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="cli-example-add-an-application-to-an-azure-batch-account"></a>CLI 예제: Azure Batch 계정에 응용 프로그램 추가

이 스크립트는 Azure Batch 풀 또는 작업에 사용할 응용 프로그램을 추가하는 방법을 보여줍니다. Batch 계정에 추가할 응용 프로그램을 설정하려면 실행 파일을 해당 종속성과 함께 zip 파일로 패키지로 만듭니다. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0.20 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/batch/add-application/add-application.sh "Add Application")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다.
표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | 저장소 계정을 만듭니다. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Batch 계정을 만듭니다. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | 추가 CLI 상호 작용을 위해 지정된 Batch 계정에 대해 인증합니다.  |
| [az batch application create](/cli/azure/batch/application#az_batch_application_create) | 응용 프로그램을 만듭니다.  |
| [az batch application package create](/cli/azure/batch/application/package#az_batch_application_package_create) | 지정된 응용 프로그램에 응용 프로그램 패키지를 추가합니다.  |
| [az batch application set](/cli/azure/batch/application#az_batch_application_set) | 응용 프로그램의 속성을 업데이트합니다.  |
| [az group delete](/cli/azure/group#az_group_delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.
