---
title: 자습서 - Azure CLI를 사용하여 사용자 지정 VM 이미지 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure CLI 2.0을 사용하여 Azure에서 사용자 지정 가상 머신 이미지를 만드는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 766e247775e61d7427b658b66948aa6699a7241a
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli-20"></a>자습서: Azure CLI 2.0을 사용하여 Azure VM의 사용자 지정 이미지 만들기

사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 응용 프로그램 사전 로드, 응용 프로그램 구성 및 기타 OS 구성과 같은 부트스트랩 구성에 사용자 지정 이미지를 사용할 수 있습니다. 이 자습서에서는 Azure Virtual Machines의 사용자 지정 이미지를 만듭니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM 프로비전 해제 및 일반화
> * 사용자 지정 이미지 만들기
> * 사용자 지정 이미지에서 VM 만들기
> * 구독에 모든 이미지 나열
> * 이미지 삭제

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

아래 단계에서는 기존 VM을 가져와서 새 VM 인스턴스를 만드는 데 사용할 수 있는 재사용 가능 사용자 지정 이미지로 변환하는 방법을 설명합니다.

이 자습서의 예제를 완료하려면 기존 가상 머신이 있어야 합니다. 필요한 경우 이 [스크립트 샘플](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md)을 사용하여 가상 컴퓨터를 만들 수 있습니다. 이 자습서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.

## <a name="create-a-custom-image"></a>사용자 지정 이미지 만들기

가상 컴퓨터의 이미지를 만들려면 프로비전을 해제하고 할당을 취소한 후 원본 VM을 일반화된 것으로 표시하여 VM을 준비해야 합니다. VM이 준비되면 이미지를 만들 수 있습니다.

### <a name="deprovision-the-vm"></a>VM 프로비전 해제 

프로비전을 해제하면 컴퓨터별 정보를 제거하여 VM을 일반화합니다. 이 일반화를 통해 단일 이미지에서 여러 VM을 배포할 수 있습니다. 프로비전을 해제하는 동안 호스트 이름이 *localhost.localdomain*으로 다시 설정됩니다. SSH 호스트 키, 이름 서버 구성, 루트 암호 및 캐시된 DHCP 임대도 삭제됩니다.

VM 프로비전을 해제하려면 Azure VM 에이전트(waagent)를 사용합니다. Azure VM 에이전트는 VM에 설치되고 Azure 패브릭 컨트롤러와의 상호 작용과 프로비전을 관리합니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](../extensions/agent-linux.md)를 참조하세요.

SSH를 사용하여 VM에 연결하고 VM 프로비전 해제 명령을 실행합니다. `+user` 인수를 사용하면 마지막으로 프로비전된 사용자 계정 및 관련 데이터도 삭제됩니다. 예제 IP 주소를 VM의 공용 IP 주소로 바꿉니다.

VM에 SSH를 사용합니다.
```bash
ssh azureuser@52.174.34.95
```
VM의 프로비전을 해제합니다.

```bash
sudo waagent -deprovision+user -force
```
SSH 세션을 닫습니다.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>할당을 취소하고 VM을 일반화된 것으로 표시

이미지를 만들려면 VM을 할당 취소해야 합니다. [az vm deallocate](/cli//azure/vm#deallocate)를 사용하여 VM의 할당을 취소합니다. 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

마지막으로, VM이 일반화된 사실을 Azure 플랫폼이 알 수 있도록 [az vm generalize](/cli//azure/vm#generalize) 명령을 사용하여 VM 상태를 일반화됨으로 설정합니다. 일반화된 VM에서만 이미지를 만들 수 있습니다.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>이미지 만들기

이제 [az image create](/cli//azure/image#create)를 사용하여 VM의 이미지를 만들 수 있습니다. 다음 예제에서는 *myVM*이라는 VM에서 *myImage*라는 이미지를 만듭니다.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>이미지에서 VM 만들기

이미지가 생겼으니, [az vm create](/cli/azure/vm#az_vm_create) 명령을 사용하여 이 이미지에서 하나 이상의 새 VM을 만들 수 있습니다. 다음 예제에서는 *myImage*라는 이미지에서 *myVMfromImage*라는 VM을 만듭니다.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

## <a name="image-management"></a>이미지 관리 

일반적인 이미지 관리 작업의 몇 가지 예제와 Azure CLI를 사용하여 완료하는 방법은 다음과 같습니다.

모든 이미지를 테이블 형식으로 이름별로 나열합니다.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

이미지 삭제를 삭제합니다. 이 예제에서는 *myResourceGroup*에서 *myOldImage*라는 이미지를 삭제합니다.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 사용자 지정 VM 이미지를 만들었습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * VM 프로비전 해제 및 일반화
> * 사용자 지정 이미지 만들기
> * 사용자 지정 이미지에서 VM 만들기
> * 구독에 모든 이미지 나열
> * 이미지 삭제

고가용성 가상 머신에 대해 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [고가용성 VM 만들기](tutorial-availability-sets.md)

