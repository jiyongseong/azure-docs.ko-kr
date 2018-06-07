---
title: "Azure CLI 스크립트 예제 - Batch 계정 만들기 - Batch 서비스 | Microsoft Docs"
description: "Azure CLI 스크립트 예제 - Batch 서비스 모드에서 Batch 계정 만들기"
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
ms.openlocfilehash: ced93032203c33dc4cda362d30192ee8eb37d944
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="cli-example-create-a-batch-account-in-batch-service-mode"></a>CLI 예제: Batch 서비스 모드에서 Batch 계정 만들기

이 스크립트는 Batch 서비스 모드에서 Azure Batch 계정을 만들고 계정의 다양한 속성을 쿼리 및 업데이트하는 방법을 보여줍니다. 기본 Batch 서비스 모드에서 Batch 계정을 만들면 Batch 서비스에서 계산 노드를 내부적으로 할당합니다. 할당된 계산 노드에는 별도의 vCPU(코어) 할당량이 적용되며 공유 키 자격 증명 또는 Azure Active Directory 토큰을 통해 계정을 인증할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 문서에서 Azure CLI 버전 2.0.20 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치](/cli/azure/install-azure-cli)를 참조하세요. 

## <a name="example-script"></a>예제 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account.sh "Create Account")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Batch 계정을 만듭니다. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | 저장소 계정을 만듭니다. |
| [az batch account set](/cli/azure/batch/account#az_batch_account_set) | Batch 계정의 속성을 업데이트합니다.  |
| [az batch account show](/cli/azure/batch/account#az_batch_account_show) | 지정된 Batch 계정의 세부 정보를 검색합니다.  |
| [az batch account keys list](/cli/azure/batch/account/keys#az_batch_account_keys_list) | 지정된 Batch 계정의 액세스 키를 검색합니다.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | 추가 CLI 상호 작용을 위해 지정된 Batch 계정에 대해 인증합니다.  |
| [az group delete](/cli/azure/group#az_group_delete) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.
