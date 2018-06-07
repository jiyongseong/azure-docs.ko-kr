---
title: "Azure CLI 스크립트 샘플 - VM 다시 시작 | Microsoft Docs"
description: "Azure CLI 스크립트 샘플 - 태그 및 ID로 VM 다시 시작"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
ms.custom: mvc
ms.openlocfilehash: a9f7cf8ba492004cb6d9e359bfb392448dfbe813
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="restart-vms"></a>VM 다시 시작

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

이 샘플에서는 일부 VM을 가져와 다시 시작하는 몇 가지 방법을 보여 줍니다.

첫 번째 방법에서는 리소스 그룹의 모든 VM을 다시 시작합니다.

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

두 번째 방법에서는 `az resouce list` 및 필터를 사용하여 태그가 지정된 VM을 리소스로 가져온 후 해당 VM을 다시 시작합니다.

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

이 샘플은 Bash 셸에서 작동합니다. Windows 클라이언트에서 Azure CLI 스크립트 실행과 관련된 옵션은 [Windows에서 Azure CLI 실행](../windows/cli-options.md)을 참조하세요.


## <a name="sample-script"></a>샘플 스크립트

이 샘플에는 세 가지 스크립트가 있습니다.
첫 번째 스크립트는 가상 머신을 프로비전합니다.
이 스크립트는 대기 없음 옵션을 사용하므로 명령은 각 VM이 프로비전할 때까지 대기하지 않고 반환됩니다.
두 번째 스크립트는 VM이 완전히 프로비전될 때까지 대기합니다.
세 번째 스크립트는 프로비전된 모든 VM을 다시 시작한 다음, 태그가 지정된 VM만 다시 시작합니다.

### <a name="provision-the-vms"></a>VM 프로비전

이 스크립트는 리소스 그룹을 만든 다음 다시 시작할 세 개의 VM을 만듭니다.
그 중 두 개의 VM에 태그가 지정됩니다.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Provision the VMs")]

### <a name="wait"></a>Wait

이 스크립트는 세 개의 VM이 모두 프로비전될 때까지 또는 그 중 하나가 프로비전되지 못할 때까지 20초마다 프로비저닝 상태를 확인합니다.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Wait for the VMs to be provisioned")]

### <a name="restart-the-vms"></a>VM 다시 시작

이 스크립트는 리소스 그룹의 모든 VM을 다시 시작한 다음, 태그가 지정된 VM만 다시 시작합니다.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Restart VMs by tag")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 머신, 가용성 집합, 부하 분산 장치 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | 가상 머신을 만듭니다.  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | `--query`와 함께 사용되어 VM이 다시 시작된 후에 프로비전되도록 하고 VM ID를 가져와 다시 시작합니다. |
| [az resource list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | `--query`와 함께 사용되어 태그를 사용하는 VM ID를 가져옵니다. |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | VM을 다시 시작합니다. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](https://docs.microsoft.com/cli/azure)를 참조하세요.

추가 가상 머신 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
