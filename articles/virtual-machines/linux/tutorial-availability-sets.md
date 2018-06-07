---
title: 자습서 - Azure에서 Linux VM을 위한 고가용성 | Microsoft Docs
description: 이 자습서에서는 Azure CLI 2.0을 사용하여 가용성 집합에서 고가용성 가상 머신을 배포하는 방법을 알아봅니다.
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: dc6fba89571515d0d2d7ed3ecc35c3065405056b
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli-20"></a>자습서: Azure CLI 2.0을 사용하여 고가용성 가상 머신 만들기 및 배포

이 자습서에서는 가용성 집합이라는 기능을 사용하여 Azure에서 VM(Virtual Machine) 솔루션의 가용성과 안정성을 향상시키는 방법에 대해 알아봅니다. 가용성 집합을 사용하면 Azure에 배포한 VM이 격리된 여러 하드웨어 클러스터에 분산되도록 할 수 있습니다. 이렇게 하면 Azure 내의 하드웨어 또는 소프트웨어 오류가 발생할 때, VM의 하위 집합에만 영향을 주며 전체 솔루션은 사용 가능한 운영 상태로 계속 유지됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가용성 집합 만들기
> * 가용성 집합에서 VM 만들기
> * 사용 가능한 VM 크기 확인

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="availability-set-overview"></a>가용성 집합 개요

가용성 집합은 해당 집합에 배치한 VM 리소스가 Azure 데이터 센터에 배포될 때 서로 간에 격리되도록 하기 위해 Azure에서 사용할 수 있는 논리적 그룹화 기능입니다. Azure는 가용성 집합 내에 배치한 VM을 여러 물리적 서버, 계산 랙, 저장 단위 및 네트워크 스위치에서 실행되도록 합니다. 하드웨어 또는 Azure 소프트웨어 오류가 발생할 경우 VM의 하위 집합에만 영향을 주는 한편 전체 응용 프로그램은 계속 유지되어 고객이 계속 사용할 수 있습니다. 가용성 집합은 안정적인 클라우드 솔루션을 구축하려고 할 때 필수적인 기능입니다.

4개의 프런트 엔드 웹 서버가 있고 데이터베이스를 호스팅하는 2개의 백 엔드 VM을 사용하는 일반적인 VM 기반 솔루션을 살펴보겠습니다. Azure를 사용하면 VM을 배포하기 전에 하나는 “웹” 계층, 다른 하나는 “데이터베이스” 계층에 대한 집합인 두 개의 가용성 집합을 정의해야 합니다. 새 VM을 만들 때 az vm create 명령에 대한 매개 변수로 가용성 집합을 지정할 수 있으며, Azure에서는 사용 가능한 집합 내에 만든 VM이 여러 물리적 하드웨어 리소스에서 자동으로 격리되도록 합니다. 웹 서버 VM 또는 데이터베이스 서버 VM 중 하나가 실행 중인 물리적 하드웨어에 문제가 있는 경우 웹 서버 VM과 데이터베이스 VM의 다른 인스턴스가 다른 하드웨어에 있기 때문에 계속 실행된다는 것을 알 수 있습니다.

Azure 내에서 신뢰할 수 있는 VM 기반 솔루션을 배포하려면 항상 가용성 집합을 사용합니다.


## <a name="create-an-availability-set"></a>가용성 집합 만들기

[az vm availability-set create](/cli/azure/vm/availability-set#az_vm_availability_set_create)를 사용하여 가용성 집합을 만들 수 있습니다. 이 예제에서는 *myResourceGroupAvailability* 리소스 그룹의 *myAvailabilitySet*이라는 가용성 집합에 대한 업데이트 및 장애 도메인 수가 *2*로 설정됩니다.

먼저 [az group create](/cli/azure/group#az-group-create)를 사용하여 리소스 그룹을 만든 다음, 가용성 집합을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

가용성 집합을 사용하면 장애 도메인 및 업데이트 도메인에서 리소스를 격리할 수 있습니다. **장애 도메인**은 서버 + 네트워크 + 저장소 리소스의 격리된 컬렉션을 나타냅니다. 위의 예제에서는 VM을 배포할 때 가용성 집합이 적어도 두 개의 장애 도메인으로 분산됩니다. 가용성 집합도 두 개의 **업데이트 도메인**으로 분산됩니다. 두 개의 업데이트 도메인은 Azure에서 소프트웨어 업데이트를 수행할 때 VM 리소스가 격리되어 VM에서 실행되는 모든 소프트웨어가 동시에 업데이트되지 않도록 합니다.


## <a name="create-vms-inside-an-availability-set"></a>가용성 집합에 포함된 VM 만들기

하드웨어 전체에 올바르게 배포되도록 하려면 VM을 가용성 집합 내에 만들어야 합니다. VM을 만든 후에는 가용성 집합에 기존 VM을 추가할 수 없습니다.

[az vm create](/cli/azure/vm#az_vm_create)를 사용하여 VM을 만들 때 `--availability-set` 매개 변수를 사용하여 가용성 집합의 이름을 지정합니다.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --no-wait
done
```

이제 가용성 집합 내에 두 개의 가상 머신이 있습니다. 동일한 가용성 집합에 있기 때문에 Azure에서는 VM 및 관련된 모든 리소스(데이터 디스크 포함)가 격리된 물리적 하드웨어에 분산되도록 합니다. 이렇게 분산되면 전체 VM 솔루션의 가용성을 훨씬 높여줍니다.

리소스 그룹 > myResourceGroupAvailability > myAvailabilitySet로 이동하여 포털에서 가용성 집합 분포를 볼 수 있습니다. 다음 예제에 표시된 것처럼 VM은 두 개의 장애 및 업데이트 도메인으로 분산됩니다.

![포털의 가용성 집합](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>사용 가능한 VM 크기 확인

VM 크기를 하드웨어에서 사용할 수 있는 가용성 집합에 나중에 추가 VM을 추가할 수 있습니다. [az vm availability-set list-sizes](/cli/azure/availability-set#az_availability_set_list_sizes)를 사용하여 하드웨어 클러스터에서 가용성 집합에 대한 사용 가능한 모든 크기를 나열합니다.

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 가용성 집합 만들기
> * 가용성 집합에서 VM 만들기
> * 사용 가능한 VM 크기 확인

가상 머신 확장 집합에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [가상 머신 확장 집합 만들기](tutorial-create-vmss.md)