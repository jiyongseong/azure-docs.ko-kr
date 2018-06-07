---
title: "Azure CLI 스크립트 샘플 - 저장소 계정 액세스 키 회전 | Microsoft Docs"
description: "Azure Storage 계정을 만들고 나서 해당 계정 액세스 키를 검색 및 회전합니다."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/22/2017
ms.author: tamram
ms.openlocfilehash: 160509a5a82b71b281d57d97e103bb4190605b7c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>저장소 계정 만들기 및 계정 액세스 키 회전

이 스크립트는 Azure Storage 계정을 만들고, 새 저장소 계정의 액세스 키를 표시하고 나서, 키를 갱신(회전)합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.sh "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, 저장소 계정 및 모든 관련된 리소스를 제거합니다.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 저장소 계정을 만들고 해당 액세스 키를 검색 및 회전합니다. 표에 있는 각 항목은 명령 관련 설명서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | 지정된 리소스 그룹에서 Azure Storage 계정을 만듭니다. |
| [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) | 지정된 계정에 대한 저장소 액세스 키를 표시합니다. |
| [az storage account keys renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) | 기본 또는 보조 저장소 계정 액세스 키를 다시 생성합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 저장소 CLI 스크립트 샘플은 [Azure Blob Storage에 대한 Azure CLI 샘플](../blobs/storage-samples-blobs-cli.md)에서 찾을 수 있습니다.
