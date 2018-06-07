---
title: "Azure CLI 스크립트 샘플 - CLI를 사용하여 관리 디스크의 스냅숏을 동일한 구독이나 다른 구독으로 복사(이동) | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - CLI를 사용하여 관리 디스크의 스냅숏을 동일한 구독이나 다른 구독으로 복사(이동)"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 904f564d2f3c0584851309780eb2ccca379534fa
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>CLI를 사용하여 관리 디스크의 스냅숏을 동일한 구독이나 다른 구독으로 복사

이 스크립트는 관리 디스크의 스냅숏을 동일한 구독이나 다른 구독으로 복사합니다. 이 스크립트를 사용하여 부모 스냅숏과 같은 지역의 다른 구독으로 스냅숏을 이동합니다.


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 원본 스냅숏의 ID를 사용하여 대상 구독에 스냅숏을 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | 스냅숏의 이름 및 리소스 그룹 속성을 사용하여 스냅숏의 모든 속성을 가져옵니다. Id 속성은 스냅숏을 다른 구독으로 복사하는 데 사용됩니다.  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_create) | 부모 스냅숏의 Id와 이름을 사용하여 다른 구독에 스냅숏을 만들어 스냅숏을 복사합니다.  |

## <a name="next-steps"></a>다음 단계

[스냅숏에서 가상 머신 만들기](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 가상 머신 및 관리 디스크 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../../app-service/app-service-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
