---
title: "Azure에서 Linux Virtual Machines 다시 배포 | Microsoft Docs"
description: "Azure에서 가상 머신을 다시 배포하여 SSH 연결 문제를 완화하는 방법"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.openlocfilehash: 484e23f7f5800faf4a1d83c2386e0c766c3a1f2d
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>새 Azure 노드로 Linux 가상 머신 다시 배포
Azure에서 Linux VM(가상 머신)에 대한 SSH 또는 응용 프로그램 액세스 문제를 해결하는 데 어려움이 있는 경우 VM을 다시 배포하는 것이 도움이 될 수 있습니다. VM을 다시 배포하면 VM이 Azure 인프라 내의 새 노드로 이동된 다음 전원이 다시 켜집니다. 모든 구성 옵션 및 관련 리소스는 그대로 유지됩니다. 이 문서에서는 Azure CLI 또는 Azure 포털을 사용하여 VM을 다시 배포하는 방법을 보여 줍니다.

> [!NOTE]
> VM을 다시 배포한 후에 임시 디스크가 손실되고 가상 네트워크 인터페이스와 연결된 동적 IP 주소가 업데이트됩니다. 

다음 옵션 중 하나를 사용하여 VM을 다시 배포할 수 있습니다. VM을 다시 배포하려면 하나의 옵션만을 선택해야 합니다.

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [Azure Portal](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Azure CLI 2.0 사용
최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치하고 [az login](/cli/azure/reference-index#az_login)을 사용하여 Azure 계정에 로그인합니다.

[az vm redeploy](/cli/azure/vm#az_vm_redeploy)를 사용하여 VM을 다시 배포합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM을 다시 배포합니다.

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Azure CLI 1.0 사용
최신 [Azure CLI 1.0](../../cli-install-nodejs.md)을 설치하고 Azure 계정에 로그인합니다. Resource Manager 모드(`azure config mode arm`)에 있는지 확인합니다.

다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM을 다시 배포합니다.

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>다음 단계
VM에 연결하는 데 문제가 있는 경우 [SSH 연결 문제 해결](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 또는 [자세한 SSH 문제 해결 단계](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 특정 도움말을 찾을 수 있습니다. VM에서 실행 중인 응용 프로그램에 액세스할 수 없는 경우 [응용 프로그램 문제 해결](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 읽어볼 수도 있습니다.

