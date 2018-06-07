---
title: CLI 2.0을 사용하여 Azure에서 Linux VM의 이미지 캡처 | Microsoft Docs
description: Azure CLI 2.0을 사용하여 대량 배포에 사용할 Azure VM의 이미지를 캡처합니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2018
ms.author: cynthn
ms.openlocfilehash: bb70b3ff84392797ce0d93b8cf5d4018ff8ebdd8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-create-an-image-of-a-virtual-machine-or-vhd"></a>가상 머신 또는 VHD의 이미지를 만드는 방법

<!-- generalize, image - extended version of the tutorial-->

Azure에서 사용할 가상 머신(VM)의 복사본을 여러 개 만들려면 VM 또는 OS VHD의 이미지를 캡처합니다. 이미지를 만들려면 개인 계정 정보를 제거해야 합니다. 이렇게 해야 여러 번 배포하는 데 더 안전합니다. 다음 단계에서는 기존 VM의 프로비전을 해제하고, 할당을 취소하고, 이미지를 만듭니다. 이 이미지를 사용하여 구독 내의 모든 리소스 그룹에서 VM을 만들 수 있습니다.

백업 또는 디버깅을 위해 기존 Linux VM의 복사본을 만들거나 온-프레미스 VM에서 특수한 Linux VHD를 업로드하려면 [사용자 지정 디스크 이미지에서 Linux VM 업로드 및 만들기](upload-vhd.md)를 참조하세요.  

**Packer**를 사용하여 사용자 지정 구성을 만들 수도 있습니다. Packer 사용에 대한 자세한 내용은 [Azure에서 Packer를 사용하여 Linux 가상 컴퓨터 이미지를 만드는 방법](build-image-with-packer.md)을 참조하세요.


## <a name="before-you-begin"></a>시작하기 전에
다음 필수 조건을 충족하는지 확인합니다.

* 관리 디스크를 사용하여 Resource Manager 배포 모델에서 만든 Azure VM이 있어야 합니다. Linux VM을 만들지 않은 경우 [포털](quick-create-portal.md), [Azure CLI](quick-create-cli.md) 또는 [Resource Manager 템플릿](create-ssh-secured-vm-from-template.md)을 사용할 수 있습니다. 필요에 따라 VM을 구성합니다. 예를 들어 [데이터 디스크를 추가하고](add-disk.md), 업데이트를 적용하고, 응용 프로그램을 설치합니다. 

* 또한 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)이 설치되어 있어 [az login](/cli/azure/reference-index#az_login)으로 Azure 계정에 로그인해야 합니다.

## <a name="quick-commands"></a>빠른 명령

이 항목에 대한 간소화된 설명이나 Azure VM에 대한 테스트, 평가 또는 학습에 대해 알아보려면 [CLI를 사용하여 Azure VM의 사용자 지정 이미지 만들기](tutorial-custom-images.md)를 참조하세요.


## <a name="step-1-deprovision-the-vm"></a>1단계: VM 프로비전 해제
Azure VM 에이전트로 VM 프로비전을 해제하여 컴퓨터 특정 파일 및 데이터를 삭제합니다. 원본 Linux VM에서 *-deprovision+user* 매개 변수와 함께 `waagent` 명령을 사용합니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](../extensions/agent-linux.md)를 참조하세요.

1. SSH 클라이언트를 사용하여 Linux VM에 연결합니다.
2. SSH 창에서 다음 명령을 입력합니다.
   
    ```bash
    sudo waagent -deprovision+user
    ```
<br>
   > [!NOTE]
   > 이미지로 캡처하려는 VM에서만 이 명령을 실행합니다. 이 과정이 이미지에서 중요한 정보가 모두 지워졌다거나 재배포에 적합하다는 것을 보장하지는 않습니다. *+user* 매개 변수는 마지막 프로비전된 사용자 계정을 제거합니다. VM에 계정 자격 증명을 유지하려면 *-deprovision*을 사용하여 사용자 계정을 현재 위치에 유지하기만 하면 됩니다.
 
3. 계속하려면 **y** 를 입력합니다. 이 확인 단계를 피하려면 **-force** 매개 변수를 추가합니다.
4. 명령이 완료된 후 **exit**를 입력합니다. 이 단계는 SSH 클라이언트를 닫습니다.

## <a name="step-2-create-vm-image"></a>2단계: VM 이미지 만들기
Azure CLI 2.0을 사용하여 VM을 일반화된 항목으로 표시하고 이미지를 캡처합니다. 다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에는 *myResourceGroup*, *myVnet*, *myVM*이 포함됩니다.

1. [az vm deallocate](/cli//azure/vm#deallocate)로 프로비전 해제한 VM의 할당을 취소합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM의 할당을 취소합니다.
   
    ```azurecli
    az vm deallocate \
      --resource-group myResourceGroup \
      --name myVM
    ```

2. [az vm generalize](/cli//azure/vm#generalize)를 사용하여 VM을 일반화된 항목으로 표시합니다. 다음 예제에서는 리소스 그룹 *myResourceGroup*에서 *myVM*이라는 VM을 일반화된 항목으로 표시합니다.
   
    ```azurecli
    az vm generalize \
      --resource-group myResourceGroup \
      --name myVM
    ```

3. 이제 [az image create](/cli/azure/image#az_image_create)로 VM 리소스의 이미지를 만듭니다. 다음 예제에서는 *myVM*이라는 VM 리소스를 사용하여 *myResourceGroup*이라는 리소스 그룹에서 *myImage*라는 이미지를 만듭니다.
   
    ```azurecli
    az image create \
      --resource-group myResourceGroup \
      --name myImage --source myVM
    ```
   
   > [!NOTE]
   > 이미지는 원본 VM과 동일한 리소스 그룹에 만들어집니다. 이 이미지에서 구독 내의 모든 리소스 그룹에 VM을 만들 수 있습니다. 관리 측면에서 VM 리소스 및 이미지에 대한 특정 리소스 그룹을 만들 수도 있습니다.
   >
   > 이미지를 영역 중복 저장소에 저장하려는 경우 [가용성 영역](../../availability-zones/az-overview.md)을 지원하고 `--zone-resilient true` 매개 변수를 포함하는 지역에 만들어야 합니다.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3단계: 캡처한 이미지로부터 새 VM 만들기
[az vm create](/cli/azure/vm#az_vm_create)로 만든 이미지를 사용하여 VM을 만듭니다. 다음 예제에서는 *myImage*라는 이미지에서 *myVMDeployed*라는 VM을 만듭니다.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>다른 리소스 그룹에서 VM 만들기 

구독 내 모든 리소스 그룹의 이미지에서 VM을 만들 수 있습니다. 이미지와 다른 리소스 그룹에 VM을 만들려면 이미지에 완전한 리소스 ID를 지정합니다. [az image list](/cli/azure/image#az_image_list)를 사용하여 이미지 목록을 봅니다. 다음 예제와 유사하게 출력됩니다.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

다음 예제에서는 이미지 리소스 ID를 지정하여 원본 이미지와 다른 리소스 그룹에 VM을 만드는 [az vm create](/cli/azure/vm#az_vm_create)를 사용합니다.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>4단계: 배포 확인

생성한 가상 머신에 SSH를 실행하여 배포를 확인하고 새 VM 사용을 시작합니다. SSH를 통해 연결하려면 [az vm show](/cli/azure/vm#az_vm_show)로 VM의 IP 주소 또는 FQDN을 찾습니다.

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>다음 단계
원본 VM 이미지에서 여러 VM을 만들 수 있습니다. 이미지를 변경해야 하는 경우: 

- 사용자 이미지에서 VM을 만듭니다.
- 모든 업데이트 또는 구성 변경 내용을 확인합니다.
- 단계에 따라 이미지를 다시 프로비전 해제, 할당 취소, 일반화하고 만듭니다.
- 향후 배포에서 이 새로운 이미지를 사용합니다. 원하는 경우에 원본 이미지를 삭제합니다.

CLI를 사용하여 VM을 관리하는 방법에 대한 자세한 내용은 [Azure CLI 2.0](/cli/azure)을 참조하세요.
